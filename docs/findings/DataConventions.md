---
title: Data Conventions
permalink: best-practices-and-findings/data-Conventions
parent: Best Practices & Findings
nav_order: 5
layout: default
---

# Data Conventions
{: .no_toc }
Checks for data conventions issues.

---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

[Back to top](#top)

---

<a name="60"/><a name="do-not-use-placeholder-rows"/>

## Using Placeholder Rows
**Check Id:** 60 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Placeholder+Rows)

A placeholder is an empty row that is created to hold the place of possible future data in the row that may or may not be necessary.

While placeholder rows do not violate database normalization rules, it is not considered a best practice to create "empty" rows. Row data should only be created when it is materialized. If the row data does not exists, it should not be inserted. If the row data is removed, the row should be hard or soft deleted. Empty rows are not free, there is overhead space allocated with placeholder rows, which can impact performance.

 Having the unnecessary placeholder rows can muddy queries that now would need to include ```IS NOT NULL``` or ```LEN(PhoneNumber) > 0``` to exclude these placeholder rows on other queries.

[Back to top](#top)

---

<a name="27"/>

## Data Should be Encrypted if Compliance Dictates
**Potential Finding:** <a name="unencrypted-data"/>Unencrypted Data<br/>
**Check Id:** 27

The table column returned for this check might have unencrypted data that you might want to have encrypted for best practices or industry specific compliance. You will need to determine if the data needs to be protected at rest, in transit or both.

**With SQL Server you have a couple choices to implement hashing or encryption**

- [SQL Server Always Encrypt 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine){:target="_blank" rel="noopener"} by Microsoft
- [SQL Server Transparent Data Encryption (TDE) 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption){:target="_blank" rel="noopener"} by Microsoft
- [Configure SQL Server Database Engine for encrypting connections 🗗](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/configure-sql-server-encryption){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="191"/>

## Use Environment-Agnostic SQL Queries and Data-Driven Design
**Check Id:** 191

Do not hard-code values into queries that will change over time. Use a data driven approach like database tables where the values can be maintained with something like a user interface. This will shift the maintenance of these values closer to the business users who are the domain experts.

Hard-coding values directly in SQL queries like specific IDs, names, or lists creates rigid, error-prone SQL code that requires manual updates and is vulnerable to inconsistencies across environments. This approach also limits adaptability and introduces risks when data changes, as it requires ongoing maintenance to ensure accuracy. Using data-driven design with tables to dynamically source values allows for flexibility, ensuring the query remains consistent, adaptable, and reflective of the latest data.

- [Hard-coding Values In SQL Code – Please Don’t
What is hard-coding? 🗗](https://sqljana.wordpress.com/2017/03/25/hard-coding-values-in-sql-code-please-dont/){:target="_blank" rel="noopener"} by SQL Jana
- [When to hardcode data inside the source code, when to use the database, and when to use a web service? 🗗](https://stackoverflow.com/questions/33576980/when-to-hardcode-data-inside-the-source-code-when-to-use-the-database-and-when/33850208#33850208){:target="_blank" rel="noopener"} by Stack Overflow

[Back to top](#top)

---

<a name="193"/>

## Not Purging Data
**Check Id:** 193 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Purging+Data)

Purging historical or obsolete data is a crucial task in maintaining a healthy SQL Server environment. It improves performance, reduces storage costs, and ensures compliance with data retention policies. The following best practices provide a structured approach to effective and safe data purging.

* Work with stakeholders to define how long data must be retained (e.g., 3 years for transactions).
* Consider legal and regulatory requirements (e.g., GDPR, HIPAA, SOX).
* Ensure purging does not break foreign key relationships, audit trails, or reports.
* Ensure indexes support the purge filter (e.g., a non-clustered index on CreateDateTime).
* Purge in batches to avoide locking and deadlocking due to lock esclation. 

```sql
CREATE PROCEDURE dbo.TableNamePurge
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        /* This will not be performant without an index that leads on CreateDateTime. */
        IF NOT EXISTS (
            SELECT
                *
            FROM
                sys.tables               AS t
            INNER JOIN sys.indexes       AS i ON i.object_id   = t.object_id
            INNER JOIN sys.columns       AS c ON c.object_id   = t.object_id
            INNER JOIN sys.index_columns AS ic ON i.object_id  = ic.object_id
                                               AND i.index_id  = ic.index_id
                                               AND c.column_id = ic.column_id
            WHERE
                t.name                   = N'TableName'
            AND SCHEMA_NAME(t.schema_id) = N'dbo'
            AND c.name                   = N'CreateDateTime'
            AND ic.key_ordinal           = 1
            AND ic.is_included_column    = 0
        )
            BEGIN
                /* This is here to tell someone to create the index. */
                RAISERROR(
                    'No index exists that leads on CreateDateTime. Process can''t run without it. Please create this index:
CREATE INDEX IX_TableName_CreateDateTime
    ON dbo.TableName (CreateDateTime ASC)
    WITH (ONLINE = ON, SORT_IN_TEMPDB = ON);'
                   ,11
                   ,1
                ) WITH NOWAIT;
                RETURN;
            END;

        /* Start the delete and archival. */
        DECLARE
            @MinimumDate datetime2(0) = '0001-01-01'
           ,@MaximumDate datetime2(0) = CAST(DATEADD(YEAR, -1, SYSDATETIME()) AS date);

        WHILE (1 = 1)
            BEGIN
                BEGIN TRY
                    BEGIN TRANSACTION;

                    /* Starting place. */
                    SELECT
                        @MinimumDate = MIN(tn.CreateDateTime)
                    FROM
                        dbo.TableName AS tn
                    WHERE
                        tn.CreateDateTime >= @MinimumDate
                    AND tn.CreateDateTime < @MaximumDate;

                    /* If there's no data to delete, this will be NULL and we can exit. */
                    IF @MinimumDate IS NULL
                        BEGIN
                            COMMIT TRANSACTION;
                            RAISERROR('No more data found to purge/archive, exiting.', 0, 1) WITH NOWAIT;
                            BREAK;
                        END;

                    /* Get the first 1000 rows with a qualifying date. */
                    WITH d
                      AS (
                          SELECT TOP (1000)
                              tn.*
                          FROM
                              dbo.TableName AS tn
                          WHERE
                              tn.CreateDateTime >= @MinimumDate
                          AND tn.CreateDateTime < @MaximumDate
                          ORDER BY
                              tn.TableNameId ASC
                      )
                    DELETE
                    d WITH (ROWLOCK)
                    /* Send output to the archive if you want it archived. */
                    OUTPUT
                        Deleted.ColumnName1
                       ,Deleted.ColumnName2
                    INTO dbo.TableNameArchive (ColumnName1, ColumnName2);

                    COMMIT TRANSACTION;

                END TRY
                BEGIN CATCH
                    IF @@TRANCOUNT > 0
                        BEGIN
                            ROLLBACK TRANSACTION;
                        END;

                    /* Handle the error here, cleanup, et cetera.
                       In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.
                    */
                    THROW;
                END CATCH;
            END;
    END;
`

- [Take Care When Scripting Batches 🗗](https://michaeljswart.com/2014/09/take-care-when-scripting-batches/){:target="_blank" rel="noopener"} by Michael J Swart
- [How to Delete Just Some Rows from a Really Big Table: Fast Ordered Deletes 🗗](https://www.brentozar.com/archive/2018/04/how-to-delete-just-some-rows-from-a-really-big-table/){:target="_blank" rel="noopener"} by Brent Ozar

[Back to top](#top)

---
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>