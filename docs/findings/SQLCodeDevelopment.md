---
title: SQL Code Conventions
permalink: best-practices-and-findings/sql-code-conventions
parent: Best Practices & Findings
nav_order: 4
layout: default
---

# SQL Code Conventions
{: .no_toc }
T-SQL code must execute properly and performant. It must be readable, well laid out and it must be robust and resilient. It must not rely on deprecated features of SQL Server or assume specific database settings.

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

<a name="73"/>

## Not Using Source Control
**Check Id:** 73 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Source+Control)

Your database objects (tables, views, stored procedures, functions, triggers, users, roles, schemas, static data, ...) should be in a source version control system.

Source control lets you see who made what changes, when, and why. Automate SQL changes during deployment. Rollback any changes you don't want. Source control helps when you develop branch features.

Most importantly, you work from a single source of truth, greatly reducing the risk of downtime at deployment and schema drift.

A [trunk-based branching strategy 🗗](https://trunkbaseddevelopment.com){:target="_blank" rel="noopener"} source control model is recommended. See [How Microsoft develops with DevOps 🗗](https://learn.microsoft.com/en-us/devops/develop/how-microsoft-develops-devops){:target="_blank" rel="noopener"}.


Gitflow is a legacy [Git workflow 🗗](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow){:target="_blank" rel="noopener"} that was originally a disruptive and novel strategy for managing Git branches. Gitflow has fallen in popularity in favor of [trunk-based workflows 🗗](https://www.atlassian.com/continuous-delivery/continuous-integration/trunk-based-development){:target="_blank" rel="noopener"}, which are now considered best practices for modern continuous software development and [DevOps 🗗](https://www.atlassian.com/devops/what-is-devops){:target="_blank" rel="noopener"} practices. Gitflow also can be challenging to use with [CI/CD 🗗](https://www.atlassian.com/continuous-delivery){:target="_blank" rel="noopener"}. 


Each developer should have their own database copy instead of a shared database. The dedicated database could be either a local database using the free edition of [SQL Server Developer 🗗](https://www.microsoft.com/en-us/sql-server/sql-server-downloads){:target="_blank" rel="noopener"} or a solution like [Redgate Test Data Manager 🗗](https://www.red-gate.com/products/test-data-manager/){:target="_blank" rel="noopener"}. See [The unnecessary evil of the shared development database 🗗](https://www.troyhunt.com/unnecessary-evil-of-shared-development){:target="_blank" rel="noopener"} by Troy Hunt.

Your SQL Server Database project choices are a SSDT ([SQL Server Data Tools 🗗](https://docs.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt){:target="_blank" rel="noopener"}) database project backed by a Git repository from Azure DevOps or GitHub depending on the client requirements or another solution like [Redgate SQL Source Control 🗗](https://www.red-gate.com/products/sql-development/sql-source-control){:target="_blank" rel="noopener"}, or [Redgate Flyway 🗗](https://flywaydb.org){:target="_blank" rel="noopener"}.

In some instances a desired-state based solution like SSDT ([SQL Server Data Tools 🗗](https://docs.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt){:target="_blank" rel="noopener"}) database project have needs beyond the [predepoloyment or postdepolyment scripts 🗗](https://learn.microsoft.com/en-us/sql/ssdt/how-to-specify-predeployment-or-postdeployment-scripts){:target="_blank" rel="noopener"} where an additional database project that executes migration type of scripts sooner in the pipeline that the database project deploy executes.

Right after the deployment activities are completed in the Production environment, there will always be a period of a few days of hypercare where [hotfixes 🗗](https://learn.microsoft.com/en-us/devops/develop/how-microsoft-develops-devops#release-hotfixes){:target="_blank" rel="noopener"}, "roll-forward", or "Fixing Forward" is possible if found necessary. Use of feature toggles are useful as an alternative to rolling forward so a new deployed feature can be disabled.

It is recommended that the database project source control be kept separate from the application code. Database and reporting team members might/should not need access to the app source code. The data and business intelligence development team might have their own changes (performance tuning, data warehouse, reporting) in a "dev" branch, but their version is not ready for production yet.

**Reasons to use a [monorepo 🗗](https://en.wikipedia.org/wiki/Monorepo){:target="_blank" rel="noopener"} for multiple projects**
- All the projects have the same permissions and access needs
- All the projects are fully aligned in terms of versioning
- All the projects must always be deployed together by the CI/CD pipeline(s)
- All the projects cannot be built separately unless they are in the same solution

If you choose to use a [monorepo 🗗](https://en.wikipedia.org/wiki/Monorepo){:target="_blank" rel="noopener"}, please ensure you have accounted for the issues that can occur if not all the bullet points are fully true.

- See [How Microsoft develops with DevOps 🗗](https://learn.microsoft.com/en-us/devops/develop/how-microsoft-develops-devops){:target="_blank" rel="noopener"} by Microsoft
- See [Gitflow workflow 🗗](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow){:target="_blank" rel="noopener"} by Atlassian
- See [Git patterns and anti-patterns for successful developers : Build 2018 🗗](https://www.youtube.com/watch?v=ykZbBD-CmP8){:target="_blank" rel="noopener"} by Microsoft
- See [Should the Database and Application projects be in the same Repository? 🗗](https://eitanblumin.com/2022/07/05/should-the-database-and-application-projects-be-in-the-same-repository){:target="_blank" rel="noopener"} by Eitan Blumin
- See [The unnecessary evil of the shared development database 🗗](https://www.troyhunt.com/unnecessary-evil-of-shared-development){:target="_blank" rel="noopener"} by Troy Hunt
- See [Schema Drift Not Handled](/best-practices-and-findings/environment-conventions#172)

[Back to top](#top)


---

<a name="74"/>

## Using ORM Instead of Stored Procedures
**Check Id:** 74 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+ORM+Instead+of+Stored+Procedures)


Put data access code (T-SQL) in the database and not in the application/ORM layer.

Utilizing an ORM or application generated database commands has downsides. ORM or app generated SQL code that sticks with basic CRUD (Create, Read, Update, Delete) are not too bad but they do not generate reasonable queries for reporting or beyond the basic CRUD commands.

With an ORM like Entity Framework you can log the SQL commands that are being generated by the software code. The generated SQL commands can be used to performance tune by a database performance tuner or turned into a database stored procedure.

- See [Entity Framework Core > Simple Logging 🗗](https://learn.microsoft.com/en-us/ef/core/logging-events-diagnostics/simple-logging){:target="_blank" rel="noopener"} by Microsoft
- See [Entity Framework 6 > Logging and intercepting database operations 🗗](https://learn.microsoft.com/en-us/ef/ef6/fundamentals/logging-and-interception){:target="_blank" rel="noopener"} by Microsoft

The benefit of using stored procedures is with query performance tuning. Eventually the queries will outgrow the ORMs ability to generate optimal queries and execution plans. You end up attempting to rewrite software code to force the ORM to produce more performant SQL code.

With stored procedures there is a lot more options to tune a query. You can split up complex SQL code so the SQL Server engine can generate better execution plans, convert to dynamic SQL, use different hints, trace flags, and isolation levels, etc.

The free opensource [sp_CRUDGen 🗗](https://github.com/kevinmartintech/sp_CRUDGen){:target="_blank" rel="noopener"} (by Kevin Martin) can be utilized to create 11 different stored procedures from basic your Create, Read, Update, Delete, Upsert stored procedures to extremely advanced safe dynamic Search stored procedures otherwise known as optional parameters, kitchen sink, Swiss army knife, catch-all queries. The generated stored procedure code utilizes the SQL Server community best practices.

- See [Database-First: Stored Procedure in Entity Framework 🗗](https://www.entityframeworktutorial.net/stored-procedure-in-entity-framework.aspx){:target="_blank" rel="noopener"} by entityframeworktutorial.net
- See [Simple C# Data Access with Dapper and SQL - Minimal API Project Part 1 🗗](https://www.youtube.com/watch?v=dwMFg6uxQ0I){:target="_blank" rel="noopener"} by Tim Corey
- See [Simple C# Data Access with Dapper and SQL - Minimal API Project Part 2 🗗](https://www.youtube.com/watch?v=5tYSO5mAjXs){:target="_blank" rel="noopener"} by Tim Corey


[Back to top](#top)

---

<a name="75"/>

## Data Warehouse Date & Time Key Pattern
**Check Id:** 75 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Data+Warehouse+Date+and+Time+Key+Pattern)

This data warehouse date & time key pattern can be used with the [Date-Time-Number-Dimensions 🗗](https://github.com/kevinmartintech/Date-Time-Number-Dimension){:target="_blank" rel="noopener"} (by Kevin Martin) tables. The grain of the time dimension rows is in seconds.

The primary keys in the date and time dimension tables are integers and the T-SQL below extracts and converts them.

If the datetime column in the source table is a datetimeoffset data type you can remove the first 'AT TIME ZONE'.

```sql
SELECT
    [Date Key] = 
		ISNULL(
			CAST(
				CONVERT(char(8), 
					CAST(
						CAST(
							GETDATE() AS datetime2(7) /* use the table date column instead of GETDATE() here */
						) 
						AT TIME ZONE 'UTC' /* use this 1st AT TIME ZONE to set the offset if the data type is not using datetimeoffset(7) */
						AT TIME ZONE 'Central Standard Time' /* this 2nd AT TIME ZONE converts into the time zone */
						AS DATETIME
					), 112
				) AS INT
			), -1
		)
   ,[Time Key] = 
		REPLACE(
			CONVERT(varchar(8), 
				CAST(
					CAST(
						GETDATE() AS datetime2(7) /* use the table date column instead of GETDATE() here */
					) 
					AT TIME ZONE 'UTC' /* use this 1st AT TIME ZONE to set the offset if the data type is not using datetimeoffset(7) */
					AT TIME ZONE 'Central Standard Time' AS TIME /* this 2nd AT TIME ZONE converts into the time zone */
				), 108
			), ':', ''
		);
```

- See [Using datetime Instead of datetimeoffset](/best-practices-and-findings/data-type-conventions#70)

[Back to top](#top)

---

<a name="184"/>

## Not Inserting Rows Into a Temporary Table
**Check Id:** 184 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Inserting+Rows+Into+a+Temporary+Table)

Ensure you insert rows into a temporary table for performance considerations for [table-valued parameter 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/tables/use-table-valued-parameters-database-engine){:target="_blank" rel="noopener"}, [JSON 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server){:target="_blank" rel="noopener"}, [XML 🗗](https://docs.microsoft.com/en-us/sql/t-sql/xml/nodes-method-xml-data-type){:target="_blank" rel="noopener"}, [comma-separated list 🗗](https://docs.microsoft.com/en-us/sql/t-sql/functions/string-split-transact-sql){:target="_blank" rel="noopener"} or [STRING_SPLIT()  🗗](https://learn.microsoft.com/en-us/sql/t-sql/functions/string-split-transact-sql?view=sql-server-ver16){:target="_blank" rel="noopener"} functions.

```sql
CREATE OR ALTER PROCEDURE Application.PersonCreateMultiplex (@JSON nvarchar(MAX))
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        IF ISJSON(@JSON) = 1
            BEGIN
                /* Create temporary table to store the JSON rows */
                CREATE TABLE #Person (
                    Title     nvarchar(10)  NULL
                   ,FirstName nvarchar(100) NOT NULL
                   ,LastName  nvarchar(100) NOT NULL
                   ,Suffix    nvarchar(10)  NULL
                   ,NickName  nvarchar(100) NULL
                );

                /* Insert into temporary table to store the JSON rows */
                INSERT INTO #Person (Title, FirstName, LastName, Suffix, NickName)
                SELECT
                    Title
                   ,FirstName
                   ,LastName
                   ,Suffix
                   ,NickName
                FROM
                    OPENJSON(@JSON)
                        WITH (
                            Title nvarchar(10)
                           ,FirstName nvarchar(100)
                           ,LastName nvarchar(100)
                           ,Suffix nvarchar(10)
                           ,NickName nvarchar(100)
                        );

                /* Perform the create (insert) */
                INSERT INTO Application.Person (Title, FirstName, LastName, Suffix, NickName)
                SELECT
                    P.Title
                   ,P.FirstName
                   ,P.LastName
                   ,P.Suffix
                   ,P.NickName
                FROM
                    #Person AS P;

            END;
        ELSE
            BEGIN
                ; THROW 52001, 'JSON is not valid!', 1;
            END;
    END;
```


- See [How to Pass a List of Values Into a Stored Procedure 🗗](https://www.brentozar.com/archive/2020/02/how-to-pass-a-list-of-values-into-a-stored-procedure/){:target="_blank" rel="noopener"} By Brent Ozar
- See [Using SQL Server’s Table Valued Parameters 🗗](https://www.brentozar.com/archive/2014/02/using-sql-servers-table-valued-parameters/){:target="_blank" rel="noopener"} By Brent Ozar
- See [Statistics Matter on Temp Tables, Too 🗗](https://www.brentozar.com/archive/2014/02/statistics-matter-on-temp-tables-too/){:target="_blank" rel="noopener"} By Brent Ozar
- See [Use Table-Valued Parameters (Database Engine) 🗗](https://learn.microsoft.com/en-us/sql/relational-databases/tables/use-table-valued-parameters-database-engine){:target="_blank" rel="noopener"} by Microsoft
- See [Using User-Defined Tables 🗗](https://learn.microsoft.com/en-us/sql/relational-databases/server-management-objects-smo/tasks/using-user-defined-tables){:target="_blank" rel="noopener"}  by Microsoft
- See [Split strings the right way – or the next best way 🗗](https://sqlperformance.com/2021/09/t-sql-queries/split-strings){:target="_blank" rel="noopener"} by Aaron Bertrand

[Back to top](#top)

---

<a name="189"/>

## Use of IF EXISTS or IF NOT EXISTS Before DML Statements
**Check Id:** 189 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Use+of+IF+EXISTS+or+IF+NOT+EXISTS+Before+DML+Statements)

Using `IF EXISTS` checks followed by `UPDATE` or `DELETE` statements can lead to race conditions in a concurrent environment. Data may change between the existence check and the DML operation, causing inconsistent or unexpected results.

A race condition occurs when multiple transactions access and manipulate the same data concurrently, and the final outcome depends on the timing of their execution. 
conditions.

**Don't do this:**
```sql
IF EXISTS (SELECT 1 FROM dbo.Person WHERE /* conditions */)
BEGIN
    UPDATE dbo.Person
    SET /* updates */
    WHERE /* conditions */;
END
```

**Do This:**
Combine the Check and Operation: Remove the `IF EXISTS` check when it's not necessary. The `UPDATE` will have no effect if no rows match.

```sql
UPDATE dbo.Person
SET /* updates */
WHERE /* conditions */;
```

**Or Do This:**
Use Transactions and Locking: If the check is essential, wrap the statements in a transaction and use appropriate locking hints to prevent data changes between operations.

```sql
BEGIN TRANSACTION;

IF EXISTS (SELECT 1 FROM dbo.Person WITH (UPDLOCK, HOLDLOCK) WHERE /* conditions */)
BEGIN
    UPDATE dbo.Person
    SET /* updates */
    WHERE /* conditions */;
END

COMMIT TRANSACTION;
```

- See [UPSERT Pattern](/best-practices-and-findings/sql-code-conventions#76)

[Back to top](#top)

---

<a name="76"/>

## UPSERT Pattern
**Check Id:** 76 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=UPSERT+Pattern)

Locating the row to confirm it exists is doing the work twice.

```sql
IF EXISTS (SELECT * FROM dbo.Person WHERE PersonId = @PersonId)
    BEGIN
        UPDATE dbo.Person SET FirstName = @FirstName WHERE PersonId = @PersonId;
    END;
ELSE
    BEGIN
        INSERT dbo.Person (PersonId, FirstName) VALUES (@PersonId, @FirstName);
    END;
```

- See [SQL Server UPSERT Patterns and Antipatterns 🗗](https://michaeljswart.com/2017/07/sql-server-upsert-patterns-and-antipatterns/){:target="_blank" rel="noopener"} by Michael J Swart
- See [Please stop using this UPSERT anti-pattern 🗗](https://sqlperformance.com/2020/09/locking/upsert-anti-pattern){:target="_blank" rel="noopener"} by Aaron Bertrand
- See [Use of IF EXISTS or IF NOT EXISTS Before DML Statements](/best-practices-and-findings/sql-code-conventions#189)

**Use this UPSERT pattern when a row update is more likely:** Don't worry about checking for a rows existence just perform the update.

Consider using [sp_CRUDGen 🗗](https://github.com/kevinmartintech/sp_CRUDGen){:target="_blank" rel="noopener"} (by Kevin Martin) to generate an UPSERT stored procedure at least as starting point.

```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    UPDATE
        dbo.Person WITH (UPDLOCK, SERIALIZABLE)
    SET
        FirstName = 'Kevin'
    WHERE LastName = 'Martin';

    IF @@ROWCOUNT = 0
    BEGIN
        INSERT dbo.Person (FirstName, LastName) VALUES ('Kevin', 'Martin');
    END;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
    BEGIN
        ROLLBACK TRANSACTION;
    END;

    THROW;
END CATCH;
```

**Use this UPSERT pattern when a row insert is more likely:** Don't worry about checking for a rows existence just perform the insert.

```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    INSERT dbo.Person
    (
        FirstName,
        LastName
    )
    SELECT
        'Kevin',
        'Martin'
    WHERE NOT EXISTS
    (
        SELECT
            1
        FROM dbo.Person WITH (UPDLOCK, SERIALIZABLE)
        WHERE LastName = 'Martin'
    );

    IF @@ROWCOUNT = 0
    BEGIN
        UPDATE dbo.Person SET FirstName = 'Kevin' WHERE LastName = 'Martin';
    END;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
    BEGIN
        ROLLBACK TRANSACTION;
    END;

    THROW;
END CATCH;
```

**Use this UPSERT pattern to allow the client application to handle the exception:** Ensure you handle the exception in your code.

```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRANSACTION;

BEGIN TRY
    INSERT dbo.Person (FirstName, LastName) VALUES ('Kevin', 'Martin');
END TRY
BEGIN CATCH
    UPDATE dbo.Person SET FirstName = 'Kevin' WHERE LastName = 'Martin';
    THROW;
END CATCH;

COMMIT TRANSACTION;
```
**Use this UPSERT pattern for upserting multiple rows:** You can use a [table-valued parameter 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/tables/use-table-valued-parameters-database-engine){:target="_blank" rel="noopener"}, [JSON 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server){:target="_blank" rel="noopener"}, [XML 🗗](https://docs.microsoft.com/en-us/sql/t-sql/xml/nodes-method-xml-data-type){:target="_blank" rel="noopener"} or [comma-separated list 🗗](https://docs.microsoft.com/en-us/sql/t-sql/functions/string-split-transact-sql){:target="_blank" rel="noopener"}. 

For JSON, XML or comma-separated list ensure you insert the rows into a temporary table for performance considerations.

- See [Not Inserting Rows Into a Temporary Table](sql-code-conventions#184)

```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    /***************************************************************
    ** Create temp table to store the updates
    ****************************************************************/
    CREATE TABLE #Update
    (
        FirstName varchar(50) NOT NULL,
        LastName varchar(50) NOT NULL
    );

    INSERT INTO #Update
    (
        FirstName,
        LastName
    )
    VALUES 
        ('Kevin', 'Martin'),
        ('Jean-Luc', 'Picard');


    /***************************************************************
    ** Perform Updates (WHEN MATCHED)
    ****************************************************************/
    UPDATE
        P WITH (UPDLOCK, SERIALIZABLE)
    SET
        P.FirstName = U.FirstName
    FROM dbo.Person AS P
    INNER JOIN #Update AS U
        ON P.LastName = U.LastName;


    /***************************************************************
    ** Perform Inserts (WHEN NOT MATCHED [BY TARGET])
    ****************************************************************/
    INSERT dbo.Person
    (
        FirstName,
        LastName
    )
    SELECT
        U.FirstName,
        U.LastName
    FROM #Update AS U
    WHERE NOT EXISTS
    (
        SELECT * FROM dbo.Person AS P WHERE P.LastName = U.LastName
    );


    /***************************************************************
    ** Perform Deletes (WHEN NOT MATCHED BY SOURCE)
    ****************************************************************/
    DELETE P
    FROM dbo.Person AS P
    LEFT OUTER JOIN #Update AS U
        ON P.LastName = U.LastName
    WHERE U.LastName IS NULL;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
    BEGIN
        ROLLBACK TRANSACTION;
    END;

    THROW;
END CATCH;
```

**Do not use this UPSERT pattern:** It will produce primary key violations when run concurrently.

```sql
IF EXISTS (SELECT * FROM dbo.Person AS P WHERE P.LastName = 'Martin')
    BEGIN
        UPDATE dbo.Person 
		SET FirstName = 'Kevin' 
		WHERE LastName = 'Martin';
    END;
ELSE
    BEGIN
        INSERT dbo.Person (FirstName, LastName) 
		VALUES ('Kevin', 'Martin');
    END;
```

**This UPSERT pattern can be problematic:** In general, do not use MERGE statements in transactional (OLTP) databases, even though they are valid in ETL processes. If you do encounter MERGE statements in OLTP databases, be sure to address potential concurrency issues as described below. MERGE can be used for ETL processing if it is assured to NOT be run concurrently. Hugo below says don't blindly use MERGE. Get informed on the things that can be problematic.

- See [An update on MERGE 🗗](https://sqlserverfast.com/blog/hugo/2023/09/an-update-on-merge/){:target="_blank" rel="noopener"} by Hugo Kornelis
- See [What To Avoid If You Want To Use MERGE 🗗](https://michaeljswart.com/2021/08/what-to-avoid-if-you-want-to-use-merge/#:~:text=So%20just%20to,MERGE){:target="_blank" rel="noopener"} by Michael J. Swart
- See [Use Caution with SQL Server's MERGE Statement 🗗](https://www.mssqltips.com/sqlservertip/3074/use-caution-with-sql-servers-merge-statement/#:~:text=function%20as%20expected.-,Conclusion,-I%20am%20not){:target="_blank" rel="noopener"} by Aaron Bertrand


```sql
MERGE INTO dbo.Person WITH (HOLDLOCK) AS T
USING (
	SELECT FirstName = 'Kevin', LastName = 'Martin'
) AS S
ON (S.LastName = T.LastName)
    WHEN MATCHED AND EXISTS (
        SELECT
             S.FirstName
            ,S.LastName
        EXCEPT
        SELECT
             T.FirstName
            ,T.LastName
    ) THEN
    UPDATE SET
        T.FirstName = S.FirstName
WHEN NOT MATCHED BY TARGET THEN
    INSERT (FirstName, LastName)
    VALUES
         (S.FirstName, S.LastName)
WHEN NOT MATCHED BY SOURCE THEN
        DELETE;
```

#### UPSERT Use case exception
 If an update is not wanted for a reason like system-versioned temporal table history pollution the ```WITH (UPDLOCK, SERIALIZABLE)``` hint below should be used.

```UPDLOCK``` is used to protect against deadlocks and let other session wait instead of falling victim a deadlock event. ```SERIALIZABLE``` will protect against changes to the data in the transaction and ensure no other transactions can modify data that has been read by the current transaction until the current transaction completes.

This method will not prevent a race condition and the last update will win.

```sql
BEGIN TRY
    BEGIN TRANSACTION;

    IF EXISTS (SELECT * FROM dbo.Person WITH (UPDLOCK, SERIALIZABLE) WHERE PersonId = @PersonId)
        BEGIN
            UPDATE dbo.Person SET FirstName = @FirstName WHERE PersonId = @PersonId;
        END;
    ELSE
        BEGIN
            INSERT dbo.Person (PersonId, FirstName) VALUES (@PersonId, @FirstName);
        END;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
    BEGIN
        ROLLBACK TRANSACTION;
    END;

    THROW;
END CATCH;
```

[Back to top](#top)

---

<a name="77"/>

## Using ORDER BY
**Check Id:** 77 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+ORDER+BY)

SQL Server is the second most expensive sorting system, remind the developer they can sort in the application layer which does not have a cost per core.

If you need to sort data, try doing it in the app tier instead. Developers are really good about scaling out application work, and their app servers don’t cost $7K per core. If the query doesn’t have a TOP, then it probably shouldn’t have an ORDER BY.

**2022 Pricing**

- Oracle Enterprise Edition = $47,500 per core
- Microsoft SQL Server Enterprise Edition = $7,561 per core 
- Microsoft SQL Server Standard Edition = $1,972 per core 
- ⬇
- Microsoft Access $160

<br/>

- See [How to Think Like the SQL Server Engine: Adding an ORDER BY 🗗](https://www.brentozar.com/archive/2019/10/how-to-think-like-the-sql-server-engine-adding-an-order-by/){:target="_blank" rel="noopener"} by Brent Ozar


[Back to top](#top)

---

<a name="78"/>

## Not Parameterized Queries
**Check Id:** 78 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Parameterized+Queries)

Parameterized queries are an effective defense against SQL injection attacks and significantly reduce the risk of such attacks. Parameterized queries help prevent SQL injection as they separate user input from the SQL query itself, ensuring that input is treated as data rather than executable code. This makes it much more challenging for attackers to inject malicious SQL code.

Queries should also be parameterized so SQL Server can reuse the execution plan. You application code is either hardcoding the query values in the WHERE clause or you are using some sort of dynamic T-SQL and not doing it correctly. Utilizing parameterized queries in SQL Server optimizes the reuse of execution plans minimizing the CPU cycles needed for query compilation.

A temporary fix is to force parameterization until you can refactor the code and include some @parameters.

- See [System.Data.SqlClient > SqlCommand > Parameters Property 🗗](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlcommand.parameters){:target="_blank" rel="noopener"} by Microsoft
- See [Microsoft.Data.SqlClient > SqlParameter Class 🗗](https://learn.microsoft.com/en-us/dotnet/api/microsoft.data.sqlclient.sqlparameter){:target="_blank" rel="noopener"} by Microsoft
- See [Using Parameters With Dapper 🗗](https://www.learndapper.com/parameters){:target="_blank" rel="noopener"} by Lean Dapper
- See [Entity Framework Core > SQL Queries > Passing parameters 🗗](https://learn.microsoft.com/en-us/ef/core/querying/sql-queries#passing-parameters){:target="_blank" rel="noopener"} by Microsoft


[Back to top](#top)

---

<a name="79"/>

## Cursors
**Check Id:** 79 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Cursors)


### Overview
Even though you'll hear DBAs and other experts say, "never use cursors!", there are a few cases were cursors come in handy and there are a few important pointers.

SQL Server originally supported cursors to more easily port dBase II applications to SQL Server, but even then, you can sometimes use a `WHILE` loop (See [Using WHILE Loop](/best-practices-and-findings/sql-code-conventions#80)) as an effective substitute. Modern versions of SQL Server provide window functions and the CROSS/OUTER APPLY syntax to cope with some of the traditional valid uses of the cursor.

### Valid Use Cases
- Executing a complex stored procedure or series of stored procedures based on a set of data. It is true this can be handled with a `WHILE` loop and grabbing each row from the database, but a read-only, fast-forward cursor well and can be easier to manage.
- Import scripts

### Cursor Type
It is likely a bad idea to use any cursor other than one that is read-only and fast-forward. However, you need to declare your cursor correctly in order to create the right type:
`DECLARE MyCursor CURSOR LOCAL FAST_FORWARD FOR`

### Full Cursor Syntax

```sql
DECLARE
    @MyId          int
   ,@MyName        nvarchar(50)
   ,@MyDescription nvarchar(MAX);

DECLARE MyCursor CURSOR LOCAL FAST_FORWARD FOR
    SELECT
        MT.MyId
       ,MT.MyName
       ,MT.MyDescription
    FROM
        dbo.MyTable AS MT
    WHERE
        MT.DoProcess = 1;

OPEN MyCursor;

FETCH NEXT FROM MyCursor
INTO
    @MyId
   ,@MyName
   ,@MyDescription;

WHILE @@FETCH_STATUS = 0
    BEGIN
        --Do something or a series of things for each row
        DECLARE @Result int;
        EXEC @Result = dbo.SomeStoredProcedure @MyId = @MyId;

        FETCH NEXT FROM MyCursor
        INTO
            @MyId
           ,@MyName
           ,@MyDescription;
    END;

CLOSE MyCursor;
DEALLOCATE MyCursor;
```

[Back to top](#top)

---

<a name="80"/>

## Using WHILE Loop
**Check Id:** 80 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+WHILE+Loop)

`WHILE` loop is really a type of cursor. Although a `WHILE` loop can be useful for several inherently procedural tasks, you can usually find a better relational way of achieving the same results. The database engine is heavily optimized to perform set-based operations rapidly.

Here is a `WHILE` loop pattern. You might be able to create a SQL statement that does a bulk update instead.

```sql
CREATE TABLE #Person (
    PersonId        int           NOT NULL IDENTITY(1, 1) PRIMARY KEY
   ,FirstName       nvarchar(100) NOT NULL
   ,LastName        nvarchar(128) NOT NULL
   ,IsProcessed     bit           NOT NULL DEFAULT (0)
);

INSERT INTO
    #Person (FirstName, LastName, IsProcessed)
VALUES
     (N'Joel', N'Miller', 0);

DECLARE
    @PersonId  INT
   ,@FirstName nvarchar(100)
   ,@LastName  nvarchar(100);

WHILE EXISTS (SELECT * FROM #Person WHERE IsProcessed = 0)
    BEGIN
        SELECT TOP (1)
               @PersonId  = P.PersonId
              ,@FirstName = P.FirstName
              ,@LastName  = P.LastName
        FROM
            #Person AS P
        WHERE
            P.IsProcessed = 0
        ORDER BY
            P.PersonId ASC;

        UPDATE
            dbo.Person
        SET
            FirstName = @FirstName
           ,LastName = @LastName
        WHERE
            PersonId = @PersonId;

        UPDATE #Person SET IsProcessed = 1 WHERE PersonId = @PersonId;
    END;
```

[Back to top](#top)

---

<a name="81"/>

## Temporary Tables and Table Variables
**Check Id:** 81 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Temporary+Tables+and+Table+Variables)

Use Temporary Tables and not Table Variables.

- There is optimization limitations like lack of statistics that very frequently lead to performance issues. The advice of "use table variables if you have less than NNN rows" is flawed. It might seem like temporary tables are performant, but they are not scalable with a couple more years of data.
- There are two use cases for table variable and are infrequently called for.
  1. Extremely highly called code where recompiles from temporary table activity is a problem
  2. Audit scenarios when you need to keep data after a transaction is rolled back.

**Do This:**

``` sql
CREATE TABLE #UseMe (
    UseMeId   int           NOT NULL IDENTITY(1, 1) PRIMARY KEY
   ,FirstName nvarchar(100) NOT NULL
   ,LastName  nvarchar(100) NOT NULL
);
```

**Instead Of:**

``` sql
DECLARE @DoNotUseMe table (
    DoNotUseMeId int           NOT NULL IDENTITY(1, 1) PRIMARY KEY
   ,FirstName    nvarchar(100) NOT NULL
   ,LastName     nvarchar(100) NOT NULL
);
```

[Back to top](#top)

---

<a name="82"/>

## Using Hints
**Check Id:** 82 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Hints)

Because the SQL Server Query Optimizer typically selects the best execution plan for a query, we recommend that [hints 🗗](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table){:target="_blank" rel="noopener"} (by Microsoft) be used only as a last resort by experienced developers and database administrators.

[Back to top](#top)

---

<a name="83"/>

## Parameters or Variables Do Not Match Column Data Type
**Check Id:** 83 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Parameters+or+Variables+Do+Not+Match+Column+Data+Type)

Parameters and variables should match the column data type, length, and precision.

Data type mismatches can lead to unexpected behavior, errors, or performance issues. 

The example below is how not to create parameters and variables with mismatching types and lengths.

```sql
CREATE OR ALTER PROCEDURE dbo.BusinessEntityAction (
    @FirstName AS nvarchar(100) /* ← 100 characters */
)
AS
    BEGIN
        SET NOCOUNT ON;
                                              /* ↓ 50 characters */
        SELECT LastName FROM dbo.Person WHERE FirstName = @FirstName;


                            /* ↓ 100 characters and varchar() */
        DECLARE @LastName AS varchar(100) = N'martin'

                                              /* ↓ 50 characters and nvarchar() */
        SELECT FirstName FROM dbo.Person WHERE LastName = @LastName;

    END;
```

- See [Mixing Data Types in JOIN or WHERE Clauses](/best-practices-and-findings/sql-code-conventions#103)
- See [Using a Non-SARGable Expression in a WHERE Clause](/best-practices-and-findings/sql-code-conventions#100)

[Back to top](#top)

---

<a name="84"/>

## When to Breakdown Complex Queries
**Check Id:** 84 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=When+to+Breakdown+Complex+Queries)

Source: [When To Break Down Complex Queries 🗗](https://docs.microsoft.com/en-us/archive/blogs/sqlcat/when-to-break-down-complex-queries){:target="_blank" rel="noopener"}. (by Microsoft)

Microsoft SQL Server is able to create very efficient query plans in most cases. However, there are certain query patterns that can cause problems for the query optimizer; this paper describes four of these patterns. These problematic query patterns generally create situations in which SQL Server must either make multiple passes through data sets or materialize intermediate result sets for which statistics cannot be maintained. Or, these patterns create situations in which the cardinality of the intermediate result sets cannot be accurately calculated.

Breaking these single queries into multiple queries or multiple steps can sometimes provide SQL Server with an opportunity to compute a different query plan or to create statistics on the intermediate result set. Taking this approach instead of using query hints lets SQL Server continue to react to changes in the data characteristics as they evolve over time.

Although the query patterns discussed in this paper are based on customer extract, transform, and load (ETL) and report jobs, the patterns can also be found in other query types.

This paper focuses on the following four problematic query patterns:

### OR logic in the WHERE clause
In this pattern, the condition on each side of the OR operator in the WHERE or JOIN clause evaluates different tables. This can be resolved by use of a UNION operator instead of the OR operator in the WHERE or JOIN clause. 

- See [Problematic Use of OR 🗗](https://docs.microsoft.com/en-us/archive/blogs/sqlcat/when-to-break-down-complex-queries#problematic-use-of-or){:target="_blank" rel="noopener"} by Microsoft

### Aggregations in intermediate results sets
This pattern has joins on aggregated data sets, which can result in poor performance. This can be resolved by placing the aggregated intermediate result sets in temporary tables.

- See [Query Anti-Pattern 2: Aggregations in Intermediate Result Sets 🗗](https://docs.microsoft.com/en-us/archive/blogs/sqlcat/when-to-break-down-complex-queries#:~:text=Query%20Anti%2DPattern%202%3A%20Aggregations%20in%20Intermediate%20Result%20Sets){:target="_blank" rel="noopener"} by Microsoft

### A large number of very complex joins
This pattern has a large number of joins, especially joins on ranges, which can result in poor performance because of progressively degrading estimates of cardinality. This can be resolved by breaking down the query and using temporary tables.

- See [Other Query Types to Break Down 🗗](https://docs.microsoft.com/en-us/archive/blogs/sqlcat/when-to-break-down-complex-queries#:~:text=A%20query%20with%20a%20large%20number%20of%20very%20complex%20joins){:target="_blank" rel="noopener"} by Microsoft

### A CASE clause in the WHERE or JOIN clause
This pattern has CASE operators in the WHERE or JOIN clauses, which cause poor estimates of cardinality. This can be resolved by breaking down the cases into separate queries and using the Transact-SQL IF statement to direct the flow for the conditions.

- See [Other Query Types to Break Down 🗗](https://docs.microsoft.com/en-us/archive/blogs/sqlcat/when-to-break-down-complex-queries#:~:text=%C2%B7-,A%20CASE%20clause%20in%20the%20WHERE%20or%20JOIN%20clause,-This%20case%20refers){:target="_blank" rel="noopener"} by Microsoft

An understanding of the concepts introduced in these four cases can help you identify other situations in which these or similar patterns are causing poor or inconsistent performance; you can then construct a replacement query which will give you better, more consistent performance.

[Back to top](#top)

---

<a name="85"/>

## SQL Injection Risk
**Check Id:** 85 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=SQL+Injection+Risk)

SQL injection is an attack in which malicious code is inserted into strings that are later passed to an instance of SQL Server for parsing and execution. Any procedure that constructs SQL statements should be reviewed for injection vulnerabilities because SQL Server will execute all syntactically valid queries that it receives. Even parameterized data can be manipulated by a skilled and determined attacker.

- Source [SQL Injection 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/security/sql-injection){:target="_blank" rel="noopener"} by Micrsoft
- See [Using EXECUTE](/best-practices-and-findings/sql-code-conventions#89)

[Back to top](#top)

---

<a name="86"/>

## Using Dynamic Search Conditions
**Check Id:** 86 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Dynamic+Search+Conditions)

aka. Catch-All Query or Kitchen Sink Query

If your stored procedure has multiple parameters where any one (or more) number are parameters are optional, you have a dynamic search query.

If your query is moderately complex you should use `OPTION (RECOMPILE)`. If your query is complex, you should build the query with dynamic SQL.

You will also know this based on the `WHERE` clause of the query. You will see `@ProductId IS NULL` or have parameters wrapped in an `ISNULL()`.

```sql
CREATE PROCEDURE dbo.SearchHistory
    @ProductId       int     = NULL
   ,@OrderId         int     = NULL
   ,@TransactionType char(1) = NULL
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        SELECT
            TH.ProductId
        FROM
            dbo.TransactionHistory AS TH
        WHERE
            (TH.ProductId        = @ProductId OR @ProductId IS NULL)
        AND (TH.ReferenceOrderId = @OrderId OR @OrderId IS NULL)
        AND (TH.TransactionType  = @TransactionType OR @TransactionType IS NULL)
        AND (TH.Quantity         = ISNULL(@childFilter, m.Child));
    END;
```

### IF Branches are not a Viable Fix
The problem is that we already have a query plan when we hit the `IF @CreationDate IS NULL` segment of TSQL.

```sql
CREATE OR ALTER PROCEDURE dbo.PersonSearch (@CreationDate datetime2(7) = NULL)
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        IF @CreationDate IS NULL
            BEGIN
                SET @CreationDate = '20080101';
            END;

        SELECT FirstName, LastName FROM dbo.Person WHERE CreationDate = @CreationDate;

    END;
```

For simple to moderately complex queries add `OPTION (RECOMPILE)`.

```sql
CREATE PROCEDURE dbo.SearchHistory
    @ProductId       int     = NULL
   ,@OrderId         int     = NULL
   ,@TransactionType char(1) = NULL
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        SELECT
            TH.ProductId
        FROM
            dbo.TransactionHistory AS TH
        WHERE
            (TH.ProductId        = @ProductId OR @ProductId IS NULL)
        AND (TH.ReferenceOrderId = @OrderId OR @OrderId IS NULL)
        AND (TH.TransactionType  = @TransactionType OR @TransactionType IS NULL)
        AND (TH.Quantity         = ISNULL(@childFilter, m.Child))
        OPTION (RECOMPILE)/* <-- Added this */;
    END;
```

- See [Dynamic Search Conditions in T‑SQL 🗗](http://www.sommarskog.se/dyn-search-2008.html){:target="_blank" rel="noopener"} by Erland Sommarskog

Consider using [sp_CRUDGen 🗗](https://github.com/kevinmartintech/sp_CRUDGen){:target="_blank" rel="noopener"} (by Kevin Martin) to generate the dynamic SQL query for you.

[Back to top](#top)

---

<a name="87"/>

## Using Brackets
**Check Id:** 87 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Brackets)

You might be using square brackets [] unnecessarily for object names. If object names are valid and not reserved words, there is no need to use square brackets. Use them only for invalid names.

```sql
SELECT 
    P.[FirstName]
   ,P.[MiddleName]
   ,P.[LastName]
FROM 
    [dbo].[Person] AS P

/* Use this instead */
SELECT 
    P.FirstName
   ,P.MiddleName
   ,P.LastName
FROM 
	dbo.Person AS P
```

- See [Removing the Square Bracket Decorations with SQL Prompt 🗗](https://www.red-gate.com/hub/product-learning/sql-prompt/removing-the-square-bracket-decorations-with-sql-prompt){:target="_blank" rel="noopener"} by Redgate
- See [Using Reserved Words in Name](/best-practices-and-findings/naming-conventions#4)


[Back to top](#top)

---

<a name="88"/>

## Using RANGE Instead of ROWS
**Check Id:** 88 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+RANGE+Instead+of+ROWS)

The default window function frame ```RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW``` is less performant than ```ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW```.

Source: [What is the Difference between ROWS and RANGE? 🗗](https://auntkathisql.com/2014/09/27/what-is-the-difference-between-rows-and-range){:target="_blank" rel="noopener"} by Kathi Kellenberger

[Back to top](#top)

---

<a name="89"/>

## Using EXECUTE
**Check Id:** 89 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+EXECUTE)

Do not use ```EXECUTE('SQL script');``` to execute T-SQL command. The ```EXEC()``` command is retained for backward compatibility and is susceptible to SQL injection.

Instead use ```sp_executesql``` as it allows parameter substitutions. Using parameters allows SQL Server to reuse the execution plan and avoid execution plan pollution.

[Back to top](#top)

---

<a name="90"/>

## Unnecessarily Use of Common Table Expression CTE
**Check Id:** 90 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Unnecessarily+Use+of+Common+Table+Expression+CTE)

Use a Common Table Expression (CTE) to make SQL statements easier to understand and for recursive statements. Generally anything beyond basic use of CTEs provide extra overhead and cause performance issues.

A CTE will be evaluated every time you reference it. If a CTE is referenced only once it will give the same performance as a temporary table or subquery.


[Back to top](#top)

---

<a name="91"/>

## Using NOT IN in the WHERE Clause
**Check Id:** 91 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+NOT+IN+in+the+WHERE+Clause)

When you utilize ```NOT IN``` in your ```WHERE``` clause the SQL Server optimizer will likely perform a table scan instead of an index seek. Index seeks are generally more performant than table scans.

Try rewriting the query to use ```LEFT OUTER JOIN``` and check for ```NULL``` on the right-handed side table.

[Back to top](#top)

---

<a name="92"/>

## Using Correlated Subqueries Instead of Joins
**Check Id:** 92 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Correlated+Subqueries+Instead+of+Joins)

Correlated subqueries can have a performance impact. Most correlated subqueries can be rewritten with joins or window functions and perform much faster.

- See [SQL Server Uncorrelated and Correlated Subquery 🗗](https://www.mssqltips.com/sqlservertip/6037/sql-server-uncorrelated-and-correlated-subquery/){:target="_blank" rel="noopener"} by Rick Dobson

[Back to top](#top)

---

<a name="93"/>

## Excessive Use of Parentheses
**Check Id:** 93 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Excessive+Use+of+Parentheses)

Excessive use of parentheses makes code difficult to understand and maintain.

[Back to top](#top)

---

<a name="94"/>

## Using DATALENGTH to Find the Length of a String
**Check Id:** 94 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+DATALENGTH+to+Find+the+Length+of+a+String)

```DATALENGTH()``` can give you an incorrect length with fixed length data types like ```char()```. Use ```LEN()```

**Sample Query**

```sql
DECLARE @FixedString AS char(10) = 'a';

SELECT
    WrongLength   = DATALENGTH(@FixedString)
   ,CorrectLength = LEN(@FixedString);
```

**Query Results**

|WrongLength|CorrectLength|
|-|-|
|10|1|

[Back to top](#top)

---

<a name="95"/>

## Using '== NULL' or '<> NULL' to Filter a Nullable Column
**Check Id:** 95 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+==+NULL+or+<>+NULL+to+Filter+a+Nullable+Column)

To determine whether an expression is `NULL`, use `IS NULL` or `IS NOT NULL` instead of comparison operators (such as `=` or `<>`). Comparison operators return UNKNOWN when either or both arguments are `NULL`.

[Back to top](#top)

---

<a name="96"/>

## Using the NOT IN Predicate in the WHERE Clause
**Check Id:** 96 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+the+NOT+IN+Predicate+in+the+WHERE+Clause)

Use `EXISTS` instead of `IN`. 

`EXISTS` used to be faster than `IN` when comparing data from a subquery. Using `EXISTS` would stop searching as soon as it found the first row. `IN` would collect all the results. SQL Server got smarter and treats `EXISTS` and `IN` the same way so performance is not an issue.

The `NOT IN` operator cause issues when the subquery data contains `NULL` values. `NOT EXIST` or `LEFT JOIN / IS NULL` should be used.

Option 2 (`LEFT JOIN / IS NULL`) & 3 (`NOT EXISTS`) are semantically equivalent.

```sql
/* Option 1 (NOT IN)*/
SELECT
    P.FirstName
   ,P.LastName
FROM
    dbo.Person AS P
WHERE
    P.PersonId NOT IN (SELECT L.PersonId FROM dbo.List AS L);

/* Option 2 (LEFT JOIN / IS NULL) */
SELECT
    P.FirstName
   ,P.LastName
FROM
    dbo.Person                    AS P
    LEFT OUTER JOIN dbo.List AS L ON P.PersonId = L.PersonId
WHERE
    L.PersonId IS NULL;

/* Option 3 (NOT EXISTS) */
SELECT
    P.FirstName
   ,P.LastName
FROM
    dbo.Person AS P
WHERE
    NOT EXISTS (SELECT * FROM dbo.List AS L WHERE P.PersonId = L.PersonId);
```

- See [Consider using [NOT] EXISTS instead of [NOT] IN with a subquery (PE019) 🗗](https://www.red-gate.com/hub/product-learning/sql-prompt/consider-using-not-exists-instead-not-subquery){:target="_blank" rel="noopener"} by Redgate

[Back to top](#top)

---

<a name="97"/>

## No Separate Environments
**Check Id:** 97 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=No+Separate+Environments)

You should have a Development, Testing & Production environment.

SQL Server development is a continuous process to avoid the issues caused by development and reducing the risks of blocking business.

Accidents happen! Imagine you accidentally made an update to thousands of rows that you cannot undo without hours of work. Imagine that you do not have access to original data before you changed it. Feeling scared yet? This is where a development and test environment save effort.

A development environment allows developers to program and perform test ensuring their code is correct before pushing to a centralized testing environment for UAT (User Acceptance Testing) or staging for production.

[Back to top](#top)

---

<a name="98"/>

## Using Deprecated or Discontinued Feature
**Check Id:** 98 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Deprecated+or+Discontinued+Feature)

When a feature is marked deprecated, it means:

- The feature is in maintenance mode only. No new changes will be done, including those related to addressing inter-operability with new features.
- We strive not to remove a deprecated feature from future releases to make upgrades easier. However, under rare situations, we may choose to permanently discontinue (remove) the feature from SQL Server if it limits future innovations.
- For new development work, do not use deprecated features. For existing aplications, plan to modify applications that currently use these features as soon as possible.

A discontinued feature means it is no longer available. If a discontinued feature is being used, when we upgrade to a new SQL Server version, the code will break.

See
- [Discontinued features in SQL Server 2022 🗗](https://learn.microsoft.com/en-us/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?view=sql-server-ver15#:~:text=in%20SQL%20Server.-,Discontinued%20features%20in%20SQL%20Server%202022,-(16.x)){:target="_blank" rel="noopener"} by Microsoft
- [Deprecated database engine features in SQL Server 2019 🗗](https://docs.microsoft.com/en-us/sql/database-engine/deprecated-database-engine-features-in-sql-server-version-15?view=sql-server-ver15){:target="_blank" rel="noopener"} by Microsoft
  - [Discontinued features in SQL Server 2019 🗗](https://learn.microsoft.com/en-us/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?view=sql-server-ver15#:~:text=adls-,Discontinued%20features%20in%20SQL%20Server%202019,-(15.x)){:target="_blank" rel="noopener"} by Microsoft
- [Deprecated Database Engine Features in SQL Server 2017 🗗](https://docs.microsoft.com/en-us/sql/database-engine/deprecated-database-engine-features-in-sql-server-2017?view=sql-server-ver15){:target="_blank" rel="noopener"} by Microsoft
- [Deprecated Database Engine Features in SQL Server 2016 🗗](https://docs.microsoft.com/en-us/sql/database-engine/deprecated-database-engine-features-in-sql-server-2016?view=sql-server-ver15){:target="_blank" rel="noopener"} by Microsoft
  - [Discontinued features in SQL Server 2016 🗗](https://learn.microsoft.com/en-us/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?view=sql-server-ver15#discontinued-features-in--2:~:text=2017%20(14.x).-,Discontinued%20features%20in%20SQL%20Server%202016,-(13.x)){:target="_blank" rel="noopener"} by Microsoft
- [Discontinued Database Engine Functionality in SQL Server 2014 🗗](https://docs.microsoft.com/en-us/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016?view=sql-server-2014&preserve-view=true){:target="_blank" rel="noopener"} by Microsoft
- [Discontinued Features in SQL Server 2012 🗗](https://docs.microsoft.com/en-us/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016?view=sql-server-2014&preserve-view=true#Denali){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="99"/>

## Not Using Semicolon to Terminate Statements
**Check Id:** 99 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Semicolon+to+Terminate+Statements)

Although the semicolon isn't required for most statements prior to SQL Server 2016, it will be required in a future version. If you do not include them now database migration in the future will need to add them. Terminating semicolon are required by the ANSI SQL Standard.

Continued use of deprecated features will cause database migrations to fail. An example is ```RAISERROR``` in the format ```RAISERROR 15600 'MyCreateCustomer';```  is discontinued. ```RAISERROR (15600, -1, -1, 'MyCreateCustomer');``` is the current syntax. A database will not migrate to a newer SQL Server version without refactoring the TSQL code.

For new development work, do not use deprecated features. For existing aplications, plan to modify applications that currently use these features as soon as possible. See [Deprecated database engine features in SQL Server 🗗](https://docs.microsoft.com/en-us/sql/database-engine/deprecated-database-engine-features-in-sql-server-version-15?view=sql-server-ver15#:~:text=For%20new%20development%20work%2C%20do%20not%20use%20deprecated%20features.%20For%20existing%20aplications%2C%20plan%20to%20modify%20applications%20that%20currently%20use%20these%20features%20as%20soon%20as%20possible.){:target="_blank" rel="noopener"} by Microsoft.

- See [Using Deprecated or Discontinued Feature](/best-practices-and-findings/sql-code-conventions#98)

```sql
SET NOCOUNT, XACT_ABORT ON; /* <-- semicolon goes at the end here */

DECLARE @FirstName nvarchar(100); /* <-- semicolon goes at the end here */
SET @FirstName = N'Kevin'; /* <-- semicolon goes at the end here */

SELECT LastName FROM dbo.Person WHERE FirstName = @FirstName; /* <-- semicolon goes at the end here */
SELECT Balance FROM dbo.Account; /* <-- semicolon goes at the end here */

IF EXISTS (SELECT * FROM dbo.Person)
    BEGIN
        SELECT PersonId, FirstName, LastName FROM dbo.Person; /* <-- semicolon goes at the end here */
    END; /* <-- semicolon goes at the end here */

BEGIN TRY
    BEGIN TRANSACTION; /* <-- semicolon goes at the end here */

    UPDATE dbo.Account SET Balance = 100.00 WHERE AccountId = 1; /* <-- semicolon goes at the end here */
    UPDATE dbo.Account SET Balance = 'a' WHERE AccountId = 2; /* <-- semicolon goes at the end here */

    COMMIT TRANSACTION; /* <-- semicolon goes at the end here */
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRANSACTION; /* <-- semicolon goes at the end here */
        END; /* <-- semicolon goes at the end here */
    THROW; /* <-- semicolon goes at the end here */
END CATCH; /* <-- semicolon goes at the end here */
```

[Back to top](#top)

---

<a name="100"/>

## Using a Non-SARGable Expression in a WHERE Clause
**Check Id:** 100 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+a+Non-SARGable+Expression+in+a+WHERE+Clause)

Search ARGument..able. Avoid having a column or variable used within an expression or used as a function parameter. You will get a table scan instead of an index seek which will hurt performance.

![Non-SARGable Scan vs. SARGable Seek](../Images/Using_a_Non-SARGable_Expression_in_a_WHERE_Clause.png)


Another issue with non-sargable queries besides the forced table scan is SQL Server will not be able to provide a recommended index. 

Changing the WHERE clause to not use the YEAR() function and doing a bit more typing allows SQL Server to understand what you want it to do.

![Non-SARGable Does Not Get Index Recommendation](../Images/Non-SARGable_Does_Not_Get_Index_Recommendation.png)

- See [Using Missing Indexes Recommendations](/best-practices-and-findings/sql-code-conventions#102)
- See [Using Percent at the Start of LIKE Predicate](/best-practices-and-findings/sql-code-conventions#116)


[Back to top](#top)

---

<a name="101"/>

## Using Unnecessary Functions
**Check Id:** 101 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Unnecessary+Functions)

- Use ``TRIM(string)`` instead of ``LTRIM(RTRIM(string))``
- When comparing a string for blank it is unnecessary to trim the string before the comparison. The examples below are two ways to check for parameter/variable blanks.

```sql
DECLARE @String nvarchar(100) = N'    '

IF @String = N''
BEGIN
    /* Do the thing here */
END

IF LEN(@String) = 0
BEGIN
    /* Do the thing here */
END
```
- If your database is set to 'case insensitive', you do not need to use the ``LOWER(string)`` function when comparing two strings.
   - Execute the query ``SELECT CONVERT(varchar(256), SERVERPROPERTY('collation'));`` to verify the database shows it is a 'case insensitive'  collation (\_CI\_). The default SQL Server collation is 'SQL_Latin1_General_CP1_CI_AS'.

[Back to top](#top)

---

<a name="102"/>

## Using Missing Indexes Recommendations
**Check Id:** 102 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Missing+Indexes+Recommendations)

The SQL Server Missing Indexes recommendations feature has limitations and even recommends you create indexes that already exist. It is not meant for you fine tune and only provides sometimes adequate recommendations.

You should assess the missing index recommendation but create a fine-tuned custom index that includes all that is excluded items.

- See [Books Online: Limitations of the Missing Indexes Feature 🗗](http://msdn.microsoft.com/en-us/library/ms345485(v=sql.105).aspx){:target="_blank" rel="noopener"} by Microsoft


The missing index feature has the following limitations:

* It is not intended to fine tune an indexing configuration.
* It cannot gather statistics for more than 500 missing index groups.
* It does not specify an order for columns to be used in an index.
* For queries involving only inequality predicates, it returns less accurate cost information.
* It reports only include columns for some queries, so index key columns must be manually selected.
* It returns only raw information about columns on which indexes might be missing.
* It does not suggest filtered indexes.
* It can return different costs for the same missing index group that appears multiple times in XML Showplans.
* It does not consider trivial query plans.

[Back to top](#top)

---

<a name="103"/>

## Mixing Data Types in JOIN or WHERE Clauses
**Check Id:** 103 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Mixing+Data+Types+in+JOIN+or+WHERE+Clauses)

Mixing data types cause implicit conversion and they are bad for performance. Implicit conversions ruin SARGability, makes index unusable and utilize more CPU resource than required.

In the `WHERE` clause below you will notice the "!" mark on the SELECT indicating there is an implicit conversion. In this example the EmailPromotion column is an `INT` but we are treating it like a string by performing a `LIKE`. We get a slow table scan instead of a faster index seek.

![Implicit conversions in Execution Plan](../Images/Mixing_Data_Types_in_JOIN_or_WHERE_Clauses.png)

[Back to top](#top)

---

<a name="104"/>

## Using RAISERROR Instead of THROW
**Check Id:** 104 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+RAISERROR+Instead+of+THROW)

[New applications should use THROW instead of RAISERROR 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql?redirectedfrom=MSDN#:~:text=New%20applications%20should%20use%20THROW%20instead.){:target="_blank" rel="noopener"} by Microsoft

The ```RAISERROR``` statement does not honor ```SET XACT_ABORT```. 

```RAISERROR``` never aborts execution, so execution will continue with the next statement.

A use case exception for using ```RAISERROR``` instead of ```THROW``` is for legacy compatibility reasons. ```THROW``` was introduced in SQL Server 2012 so when making modification on this code ```THROW``` can break the current code.

- See [Not Using SET XACT_ABORT ON](/best-practices-and-findings/sql-code-conventions#121)
- See [Not Using Transactions](/best-practices-and-findings/sql-code-conventions#122)

[Back to top](#top)

---

<a name="105"/>

## Not Using Semicolon THROW
**Check Id:** 105 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Semicolon+THROW)

```THROW``` is not a reserved keyword so it could be used as a transaction name or savepoint. Always use a ```BEGIN...END``` block after the ```IF``` statement in the ```BEGIN CATCH``` along with a terminating semicolon.

- See [Not Using BEGIN END](/best-practices-and-findings/sql-code-conventions#106)
- See [Not Using Semicolon to Terminate Statements](/best-practices-and-findings/sql-code-conventions#99)


**Use this:**
```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    SELECT 1 / 0;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRANSACTION;
        END;
    THROW;
END CATCH;
```

**Instead of this:**
```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    SELECT 1 / 0;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION
    THROW;
END CATCH;
```

[Back to top](#top)

---

<a name="106"/><a name="stored-procedures-not-using-begin-end"/>

## Not Using BEGIN END
**Check Id:** 106 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+BEGIN+END)

The `BEGIN...END` control-of-flow statement block is optional for stored procedures and IF statements but is required for multi-line user-defined functions. It is best to avoid confusion and be consistent and specific.

### IF Statements 

Always included ```BEGIN...END``` blocks for ```IF``` statements with a semicolon to terminate the statement. Using ```BEGIN...END``` in an ```IF``` statement and semicolon is critical in some cases. 

- See [Not Using Semicolon to Terminate Statements](/best-practices-and-findings/sql-code-conventions#99)
- See [Not Using Semicolon THROW](/best-practices-and-findings/sql-code-conventions#105)

**Use this:**
```sql
IF @@TRANCOUNT > 0
    BEGIN
        ROLLBACK TRANSACTION;
    END;
```

**Instead of this:**
```sql
IF @@TRANCOUNT > 0
    ROLLBACK TRANSACTION;
```

**Instead of this:**
```sql
IF @@TRANCOUNT > 0 ROLLBACK TRANSACTION;
```
### Stored Procedures

**Use this:**
```sql
CREATE OR ALTER PROCEDURE dbo.BusinessEntityAction
AS
    BEGIN
        SET NOCOUNT ON;

        /* [T-SQL GOES HERE] */

    END;
GO
```
**Instead of this:**
```sql
CREATE OR ALTER PROCEDURE dbo.BusinessEntityAction
AS
SET NOCOUNT ON;

/* [T-SQL GOES HERE] */

GO
```

[Back to top](#top)

---

<a name="107"/>

## SET ANSI_NULLS OFF
**Check Id:** 107 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=SET+ANSI_NULLS+OFF)

You should be using SET ANSI_NULLS ON; unless you have a good reason not to.

[Back to top](#top)

---

<a name="108"/>

## Using Types of Variable Length That Are Size 1 or 2
**Check Id:** 108 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Types+of+Variable+Length+That+Are+Size+1+or+2)

If the length of the type will be very small (size 1 or 2) and consistent, declare them as a type of fixed length, such as `char`, `nchar`, and `binary`.

When you use data types of variable length such as `varchar`, `nvarchar`, and `varbinary`, you incur an additional storage cost to track the length of the value stored in the data type. In addition, columns of variable length are stored after all columns of fixed length, which can have performance implications.

[Back to top](#top)

---

<a name="109"/>

## Data Type Without Length
**Check Id:** 109 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Data+Type+Without+Length)

Always specify lengths for a data type.

- A `varchar`, or `nvarchar` that is declared without an explicit length will use a default length. It is safer to be explicit.
- `decimal`, `numeric`. If no precision and scale are provided, SQL Server will use (18, 0), and that might not be what you want.
- Explicitly define data type default lengths instead of excluding them.
  - [n][var]char(1) or [n][var]char(30)
    - The default length is 1 when n isn't specified in a data definition or variable declaration statement. When n isn't specified with the CAST function, the default length is 30.
  - datetime2(7)
  - datetimeoffset(7)
  - float(53)
  - [var]binary(1) or [var]binary(30)
    - The default length is 1 when n isn't specified in a data definition or variable declaration statement. When n isn't specified with the CAST function, the default length is 30.

[Back to top](#top)

---

<a name="110"/>

## COALESCE vs ISNULL
**Check Id:** 110 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=COALESCE+vs+ISNULL)

The `ISNULL` function and the `COALESCE` expression have a similar purpose but can behave differently. `ISNULL` is generally the best choice when there are two

1. Because `ISNULL` is a function, it's evaluated only once. As described above, the input values for the `COALESCE` expression can be evaluated multiple times.
   1. `COALESCE`has a has a slightly more complex execution plan with being a `CASE` statement under the covers.
1. Data type determination of the resulting expression is different. `ISNULL` uses the data type of the first parameter, `COALESCE` follows the `CASE` expression rules and returns the data type of value with the highest precedence.
1. The NULLability of the result expression is different for `ISNULL` and `COALESCE`.
1. Validations for `ISNULL` and `COALESCE` are also different. For example, a NULL value for `ISNULL` is converted to int though for `COALESCE`, you must provide a data type.
1. `ISNULL` takes only two parameters. By contrast `COALESCE` takes a variable number of parameters.


- See [Deciding between COALESCE and ISNULL in SQL Server 🗗](https://www.mssqltips.com/sqlservertip/2689/deciding-between-coalesce-and-isnull-in-sql-server/){:target="_blank" rel="noopener"} by Aaron Bertrand
- See [Comparing COALESCE and ISNULL 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/coalesce-transact-sql#comparing-coalesce-and-isnull){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="111"/>

## Using ISNUMERIC
**Check Id:** 111 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+ISNUMERIC)

Avoid using the `ISNUMERIC()` function, because it can often lead to data type conversion errors. If you’re working on SQL Server 2012 or later, it’s much better to use the `TRY_CONVERT()` or `TRY_CAST()` function instead. On earlier SQL Server versions, the only way to avoid it is by using LIKE expressions.

[Back to top](#top)

---

<a name="112"/>

## Using SELECT DISTINCT
**Check Id:** 112 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+SELECT+DISTINCT)

So, while DISTINCT and GROUP BY are identical in a lot of scenarios, there is one case where the GROUP BY approach leads to better performance (at the cost of less clear declarative intent in the query itself).

You also might be using SELECT DISTINCT to mask a JOIN problem. It’s much better to determine why rows are being duplicated and fix the problem.

- See [Don’t use DISTINCT as a "join-fixer" 🗗](https://www.red-gate.com/simple-talk/databases/sql-server/t-sql-programming-sql-server/dont-use-distinct-as-a-join-fixer/){:target="_blank" rel="noopener"} by Aaron Bertrand at Redgate
- See [Performance Surprises and Assumptions : GROUP BY vs. DISTINCT 🗗](https://sqlperformance.com/2017/01/t-sql-queries/surprises-assumptions-group-by-distinct){:target="_blank" rel="noopener"} by Aaron Bertrand

[Back to top](#top)

---

<a name="113"/>

## Not Using SSIS
**Check Id:** 113 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+SSIS)

Use SSIS (SQL Server Integration Service) to move data around. You can use stored procedures and SQL Server Agent Jobs instead of SSIS to ETL data, but it will make it difficult to orchestrate tasks between different environments. 

SSIS gives you the ability to create project and package parameters that can be configured in the SSIS catalog Environments. SSIS has built in logging and Execution run reports you can access from SSMS (SQL Server Management Studio).

When it comes time to migrate to Azure you can lift and shift you SSIS packages to an Azure-SSIS Integration Runtime.


[Back to top](#top)

---

<a name="114"/>

## IN/NOT VS EXISTS/NOT EXISTS
**Check Id:** 114 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=IN+NOT+VS+EXISTS+NOT+EXISTS)

Use `EXISTS` or `NOT EXISTS` if referencing a subquery, and `IN` or `NOT IN` when using a list of literal values.


When referencing subqueries, `EXISTS` and `NOT EXISTS` are more efficient than `IN` and `NOT IN` due to how the SQL Server engine evaluates these constructs. `EXISTS` short-circuits the search as soon as a matching row is found, making it faster for large datasets. In contrast, `IN` compares all rows, which is less efficient for subqueries. Additionally, `NOT EXISTS` is more reliable than `NOT IN` because `NOT IN` fails when encountering `NULL` values. If any value in the subquery contains `NULL`, the entire comparison may return no results due to SQL's three-valued logic.

In situations involving a list of literal values, `IN` and `NOT IN` are better choices. SQL Server can optimize these comparisons by creating internal hash or range lookups, which provide faster and more efficient evaluations. Using `EXISTS` or `NOT EXISTS` for static lists is unnecessarily complex and does not offer performance benefits. Choosing `EXISTS` for subqueries and `IN` for literal lists ensures cleaner code and faster query execution.

[Back to top](#top)

---

<a name="115"/>

## Using Keyword Abbreviation
**Check Id:** 115 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Keyword+Abbreviation)

Use the full name like in DATEDIFF(**YEAR**, StartDate, EndDate) vs DATEDIFF(**YY**, StartDate, EndDate).

[Back to top](#top)

---

<a name="116"/>

## Using Percent at the Start of LIKE Predicate
**Check Id:** 116 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Percent+at+the+Start+of+LIKE+Predicate)

You will not get an index seek using the percent wildcard (%) first in your search predicate.

```sql
SELECT
    P.FirstName
   ,P.MiddleName
   ,P.LastName
FROM
    dbo.Person AS P
WHERE
    P.LastName LIKE '%son';
```

- See [Sargability: Why %string% Is Slow 🗗](https://www.brentozar.com/archive/2010/06/sargable-why-string-is-slow/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Using a Non-SARGable Expression in a WHERE Clause](/best-practices-and-findings/sql-code-conventions#100)

[Back to top](#top)

---

<a name="117"/>

## Using Unfinished Notes
**Check Id:** 117 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Unfinished+Notes)

You might still have some work to do. One of the codetags below was found.

- TODO
- FIXME
- HACK
- CLEVER
- MAGIC
- XXX
- BUG
- BUGFIX
- OPTIMIZE
- LAZY
- BROKEN
- REFACTOR
- REFACT
- RFCTR
- OOPS
- SMELL
- NEEDSWORK
- INSPECT
- RFE 
- FEETCH
- FR
- FTRQ
- FTR
- IDEA
- QUESTION
- QUEST
- QSTN
- WTF
- ???
- !!!
- NOTE 
- HELP
- TODOC 
- DOCDO
- DODOC
- NEEDSDOC
- EXPLAIN
- DOCUMENT

[Back to top](#top)

---

<a name="118"/>

## Missing Index on WHERE Clause
**Check Id:** 118 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Missing+Index+on+WHERE+Clause)

Check WHERE clauses for columns that are not included in an index. Might also want to exclude check for tables with small (5k or less) number of rows.

[Back to top](#top)

---

<a name="119"/>

## Missing Index on IN Columns
**Check Id:** 119 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Missing+Index+on+IN+Columns)

Check IN() predicates for columns that are not included in an index. Might also want to exclude check for tables with small (5k or less) number of rows.

[Back to top](#top)

---

<a name="120"/>

## Converting Dates to String to Compare
**Check Id:** 120 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Converting+Dates+to+String+to+Compare)

Don't convert dates to strings to compare. Dates should be stored with the pattern YYYY-MM-DD. Not all are and string comparisons can provide the wrong results.

[Back to top](#top)

---

<a name="121"/>

## Not Using SET XACT_ABORT ON
**Check Id:** 121 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+SET+XACT_ABORT+ON)

- When `SET XACT_ABORT ON`, if a T-SQL statement raises a run-time error, the entire transaction is terminated and rolled back.
- When `SET XACT_ABORT OFF`, in some cases only the T-SQL statement that raised the error is rolled back and the transaction continues processing. Depending upon the severity of the error, the entire transaction may be rolled back even when SET XACT_ABORT is OFF. OFF is the default setting in a T-SQL statement, while ON is the default setting in a trigger.

A use case for `SET XACT_ABORT OFF` is when debugging to trap an error.

- See [SET XACT_ABORT (Transact-SQL) 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-xact-abort-transact-sql){:target="_blank" rel="noopener"} by Microsoft
- See [Error Handling Quiz Week: Making a Turkey Sandwich with XACT_ABORT 🗗](https://www.brentozar.com/archive/2022/01/error-handling-quiz-week-making-a-turkey-sandwich-with-xact_abort/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Fundamentals of Stored Procedures 🗗](https://www.youtube.com/watch?v=kbMRv_2Z7m0){:target="_blank" rel="noopener"} by Brent Ozar
- See [SET XACT_ABORT ON 🗗](https://www.sommarskog.se/error_handling/Part1.html#jumpXACT_ABORT){:target="_blank" rel="noopener"} by Erland Sommarskog

[Back to top](#top)

---

<a name="122"/>

## Not Using Transactions
**Check Id:** 122 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Transaction+Usage)

Transactions allow for database operations to be [atomic 🗗](https://en.wikipedia.org/wiki/Atomicity_(database_systems)){:target="_blank" rel="noopener"} (by Wikipedia). A group of related SQL commands that must all complete successfully or not at all and must be rolled back.

If you are performing a funds transfer and updating multiple bank account tables with debiting one and crediting the other, they must all complete successfully or there will be an imbalance.

### Here is a basic transaction pattern.

```sql
SET NOCOUNT, XACT_ABORT ON;

/* Exclude SQL code that does not need to be inclued in the transaction. Keep transactions shrt. */

BEGIN TRY
    BEGIN TRANSACTION;

        UPDATE dbo.Account SET Balance = -100.00 WHERE AccountId = 1;
        UPDATE dbo.Account SET Balance = 'not a decimal' WHERE AccountId = 2;

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
```

### Here is transaction pattern with a custom ```THROW``` error.

```sql
SET NOCOUNT, XACT_ABORT ON;

BEGIN TRY
    DECLARE @ErrorMessageText nvarchar(2048);

    BEGIN TRANSACTION;

    /* Debit the first account */
    UPDATE dbo.Account SET Balance = -100.00 WHERE AccountId = 1;

    /* Check the first account was debited */
    IF @@ROWCOUNT <> 1
        BEGIN
            SET @ErrorMessageText = N'The debited account failed to update.';
        END;

    /* Credit the second account */
    UPDATE dbo.Account SET Balance = 100.00 WHERE AccountId = 2;

    /* Check the second account was credited */
    IF @@ROWCOUNT <> 1
        BEGIN
            SET @ErrorMessageText = N'The credited account failed to update.';
        END;

    /* Check if we have any errors to throw */
    IF @ErrorMessageText <> ''
        BEGIN
            ; THROW 50001, @ErrorMessageText, 1;
        END;

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
```

- See [Not Using SET XACT_ABORT ON](/best-practices-and-findings/sql-code-conventions#121)
- See [Not Using BEGIN END](/best-practices-and-findings/sql-code-conventions#106)
- See [Not Using Semicolon THROW](/best-practices-and-findings/sql-code-conventions#105)
- See [Using RAISERROR Instead of THROW](/best-practices-and-findings/sql-code-conventions#104)


[Back to top](#top)

---

<a name="39"/>

## Using Implicit Transactions
**Potential Finding:** <a name="using-implicit-transactions"/><br/>
**Check Id:** 39 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Implicit+Transactions)

<a name="do-not-use-set-implicit_transactions-on"/>

Do not use ```SET IMPLICIT_TRANSACTIONS ON```

The default behavior of SQL Servers is ```IMPLICIT_TRANSACTIONS OFF``` that does not keep TSQL commands open waiting for a ```ROLLBACK TRANSACTION``` or ```COMMIT TRANSACTION``` command. When OFF, we say the transaction mode is autocommit.

When ```IMPLICIT_TRANSACTIONS ON``` is used, it could appear that the command finished instantly, but there will be an exclusive lock on the row(s) until either a roll back or commit command is issued. This makes [IMPLICIT_TRANSACTIONS ON is not popular 🗗](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-implicit-transactions-transact-sql?view=sql-server-ver15#:~:text=IMPLICIT_TRANSACTIONS%20ON%20is%20not%20popular){:target="_blank" rel="noopener"} (by Microsoft) as they can cause considerable blocking and locking.

When a connection is operating in implicit transaction mode (```IMPLICIT_TRANSACTIONS ON```), the instance of the SQL Server Database Engine automatically starts a new transaction after the current transaction is committed or rolled back. You do nothing to delineate the start of a transaction; you only commit or roll back each transaction. Implicit transaction mode generates a continuous chain of transactions.

- See [Transaction locking and row versioning guide 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide){:target="_blank" rel="noopener"} > [Implicit Transactions 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver15#:~:text=and%20DB%2DLibrary.-,Implicit%20Transactions,-When%20a%20connection){:target="_blank" rel="noopener"} by Microsoft
- See [SET IMPLICIT_TRANSACTIONS ON Is One Hell of a Bad Idea 🗗](https://www.brentozar.com/archive/2018/02/set-implicit_transactions-one-hell-bad-idea){:target="_blank" rel="noopener"} by Brent Ozar


[Back to top](#top)

---

<a name="123"/>

## Error Handling
**Check Id:** 123 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Error+Handling)

There are different methodologies for handling errors that originate in a database. [New applications should use the THROW methodologies 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql?redirectedfrom=MSDN#:~:text=New%20applications%20should%20use%20THROW%20instead.){:target="_blank" rel="noopener"} (by Microsoft).

In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.

The sample stored procedures below can be used to wire up and test software code to ensure errors are bubbled up, and the user is notified, and error data is logged. After each sample stored procedure below is commented out code to execute each of them.

- See [Using RAISERROR Instead of THROW](/best-practices-and-findings/sql-code-conventions#104)
- See [Not Using Transactions](/best-practices-and-findings/sql-code-conventions#122)

### THROW Methodology (dbo.TestTHROW)
This error catching and trowing methodology is the newest. [```THROW``` 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/throw-transact-sql){:target="_blank" rel="noopener"}, introduced in SQL Server 2012 and raises an exception and transfers execution to a ```CATCH``` block of a ```TRY...CATCH``` construct.

### Return Code Methodology (dbo.TestReturnCode)
The return code methodology utilizes [```RETURN``` 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/return-transact-sql){:target="_blank" rel="noopener"}. ```RETURN```, exits unconditionally from a query or procedure. ```RETURN``` is immediate and complete and can be used at any point to exit from a procedure, batch, or statement block. Statements that follow RETURN are not executed.

When ```THROW``` is utilized, a return code is not assigned. ```RETURN``` was commonly utilized with [```RAISERROR``` 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql){:target="_blank" rel="noopener"} which never aborts execution, so ```RETURN``` could be used afterwards. (See [Using RAISERROR Instead of THROW](/best-practices-and-findings/sql-code-conventions#104)). Utilizing ```RAISERROR``` with the return code would provide context to the error that occurred to present to the user and log the error.

### Output Parameter Methodology (dbo.TestReturnCodeParameter)
This methodology utilizes stored procedure ```OUTPUT``` parameters. Here you can set a return code and a return message that is passed back to the software code to present to the user and log the error

### RAISERROR Methodology (dbo.TestRAISERROR)

[New applications should use THROW instead of RAISERROR 🗗](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql?redirectedfrom=MSDN#:~:text=New%20applications%20should%20use%20THROW%20instead.){:target="_blank" rel="noopener"}

- See [Using RAISERROR Instead of THROW](/best-practices-and-findings/sql-code-conventions#104).

```sql
/**********************************************************************************************************************/
CREATE OR ALTER PROCEDURE dbo.TestTHROW (
    @ThowSystemError     bit = 0
   ,@ThowCustomError     bit = 0
   ,@ThrowMultipleErrors bit = 0 /* Only works on SQL Server and not Azure SQL Databases. */
)
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        IF @ThowSystemError = 1
            BEGIN
                BEGIN TRY
                    BEGIN TRANSACTION;

                    SELECT DivideByZero = 1 / 0 INTO
                        #IgnoreMe1;

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

        IF @ThowCustomError = 1
            BEGIN
                DECLARE @ErrorMessageText nvarchar(2048);

                BEGIN TRY
                    BEGIN TRANSACTION;

                    SELECT JustAOne = 1 INTO
                        #IgnoreMe2;

                    /* Custom error check */
                    IF @@ROWCOUNT = 1
                        BEGIN
                            SET @ErrorMessageText = N'Two rows were expected to be impacted.';
                        END;

                    /* Another custom error check */
                    IF @@ROWCOUNT = 3
                        BEGIN
                            /* https://docs.microsoft.com/en-us/sql/t-sql/language-elements/throw-transact-sql?view=sql-server-ver15#c-using-formatmessage-with-throw */
                            SET @ErrorMessageText = N'Three rows were expected to be impacted.';
                        END;

                    /* Check if we have any errors to throw */
                    IF @ErrorMessageText <> ''
                        BEGIN
                            ; THROW 50001, @ErrorMessageText, 1;
                        END;

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
        IF @ThrowMultipleErrors = 1
            BEGIN
                BEGIN TRY
                    BACKUP DATABASE master TO DISK = 'E:\FOLDER_NOT_EXISTS\test.bak';
                END TRY
                BEGIN CATCH

                    /* Handle the error here, cleanup, et cetera.
                       In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.
                    */
                    THROW;
                END CATCH;
            END;
    END;
GO

/* Execute stored procedure
DECLARE @ReturnCode int = NULL;

EXECUTE @ReturnCode = dbo.TestTHROW
    -- Set both parameters to 0 (zero) will not throw an error --
    @ThowSystemError = 0      -- Set to 1 will throw a system generated error (8134: Divide by zero error encountered.) --
   ,@ThowCustomError = 0      -- Set to 1 will throw a custom error (50001: Two rows were expected to be impacted.) --
   ,@ThrowMultipleErrors = 0; -- Set to 1 will throw multiple error (3201: Cannot open backup device...) OR (911: Database 'AdventureWorks2019' does not exist. Make sure that the name is entered correctly.) AND (3013: BACKUP DATABASE is terminating abnormally.) NOTE: Will not work on an Azure SQL Database. --

-- NOTE: There is no return code when an error is THROWN --
SELECT ReturnCode = @ReturnCode;
GO
*/


/**********************************************************************************************************************/
CREATE OR ALTER PROCEDURE dbo.TestReturnCode (
    @ReturnDefaultReturnCode     bit = 0
   ,@ReturnSystemErrorReturnCode bit = 0
   ,@ReturnCustomReturnCode      bit = 0
)
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        IF @ReturnDefaultReturnCode = 1
            BEGIN
                /* The return code will automatically be 0 (zero) even if it is not specified */
                SELECT JustAOne = 1 INTO
                    #IgnoreMe1;
            END;

        IF @ReturnSystemErrorReturnCode = 1
            BEGIN
                BEGIN TRY
                    SELECT DivideByZero = 1 / 0 INTO
                        #IgnoreMe2;
                END TRY
                BEGIN CATCH

                    /* Handle the error here, cleanup, et cetera.
                       In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.
                    */
                    RETURN ERROR_NUMBER();
                END CATCH;
            END;

        IF @ReturnCustomReturnCode = 1
            BEGIN
                DECLARE @ReturnCode int;

                SELECT JustAOne = 1 INTO
                    #IgnoreMe3;

                IF @@ROWCOUNT <> 2
                    BEGIN
                        SET @ReturnCode = 123123;
                    END;

                /* Handle the error here, cleanup, et cetera.
                   In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.
                */
                RETURN @ReturnCode;
            END;
    END;
GO

/* Execute stored procedure
DECLARE @ReturnCode int;

EXECUTE @ReturnCode = dbo.TestReturnCode
    @ReturnDefaultReturnCode = 0 -- Set to 1 to see the default return code 0 (zero). All parameters set to 0 will also be the default return code of 0 (zero). --
   ,@ReturnSystemErrorReturnCode = 0 -- Set to 1 to see a system error return code (8134:) --
   ,@ReturnCustomReturnCode = 0; -- Set to 1 to see a custom return code (123123:) --

SELECT ReturnCode = @ReturnCode;
GO
*/


/**********************************************************************************************************************/
CREATE OR ALTER PROCEDURE dbo.TestReturnCodeParameter (
    @ReturnError   bit            = 0
   ,@ReturnCode    int            = 0   OUTPUT
   ,@ReturnMessage nvarchar(2048) = N'' OUTPUT
)
AS
    BEGIN
        SET NOCOUNT, XACT_ABORT ON;

        IF @ReturnError = 0
            BEGIN
                SET @ReturnCode = 0;
                SET @ReturnMessage = N'';
            END;
        ELSE
            BEGIN
                BEGIN TRY
                    SELECT DivideByZero = 1 / 0 INTO
                        #IgnoreMe2;
                END TRY
                BEGIN CATCH

                    /* Handle the error here, cleanup, et cetera.
                       In most cases it is best to bubble up (THROW) the error to the application/client to be displayed to the user and logged.
                    */
                    SET @ReturnCode = ERROR_NUMBER();
                    SET @ReturnMessage = ERROR_MESSAGE();
                END CATCH;
            END;
    END;
GO

/* Execute stored procedure
DECLARE
    @ReturnCode    int
   ,@ReturnMessage nvarchar(2048);

EXECUTE dbo.TestReturnCodeParameter
    @ReturnError = 0 -- Set to 1 will simulate a return code (8134) and return message (Divide by zero error encountered.) in the output parameters. --
   ,@ReturnCode = @ReturnCode OUTPUT
   ,@ReturnMessage = @ReturnMessage OUTPUT;

SELECT ReturnCode = @ReturnCode, ReturnMessage = @ReturnMessage;
GO
*/


/**********************************************************************************************************************/
CREATE OR ALTER PROCEDURE dbo.TestRAISERROR (@ThowSystemError bit = 0, @ThowCustomError bit = 0)
AS
    BEGIN
        /* New applications should use THROW instead of RAISERROR. https://docs.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql?view=sql-server-ver15#:~:text=the%20raiserror%20statement%20does%20not%20honor%20set%20xact_abort.%20new%20applications%20should%20use%20throw%20instead%20of%20raiserror. */

        /* SET NOCOUNT, XACT_ABORT ON; */
        SET NOCOUNT ON; /* The RAISERROR statement does not honor SET XACT_ABORT. */

        IF @ThowSystemError = 1
            BEGIN
                BEGIN TRY
                    BEGIN TRANSACTION;

                    SELECT DivideByZero = 1 / 0 INTO
                        #IgnoreMe1;

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
                    DECLARE @ErrorMessage nvarchar(2048) = ERROR_MESSAGE();
                    RAISERROR(@ErrorMessage, 16, 1);
                    RETURN 1;
                END CATCH;
            END;

        IF @ThowCustomError = 1
            BEGIN
                BEGIN TRY
                    BEGIN TRANSACTION;

                    SELECT DivideByZero = 1 / 0 INTO
                        #IgnoreMe2;

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
                    RAISERROR(N'My Custom Error.', 16, 1);
                    RETURN 1;
                END CATCH;
            END;
    END;
GO

/* Execute stored procedure
DECLARE @ReturnCode int;

EXECUTE @ReturnCode = dbo.TestRAISERROR
    -- Set both parameters to 0 (zero) will not throw an error --
    @ThowSystemError = 0  -- Set to 1 will throw a system generated error (50000: Divide by zero error encountered.) --
   ,@ThowCustomError = 0; -- Set to 1 will throw a system generated error (50000: My Custom Error.) --

SELECT ReturnCode = @ReturnCode
GO
*/
```

[Back to top](#top)

---

<a name="25"/>

## Scalar Function Is Not Inlineable
**Check Id:** 25

Your scalar function is not inlineable. This means it will perform poorly.

Review the [Inlineable scalar UDFs requirements 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/user-defined-functions/scalar-udf-inlining?view=sql-server-ver15#inlineable-scalar-udfs-requirements){:target="_blank" rel="noopener"} (by Microsoft) to determine what changes you can make so it can go inline. If you cannot, you should in-line your scalar function in SQL query. This means duplicate the code you would put in the scalar function in your SQL code. SQL Server 2019 & Azure SQL Database (150 database compatibility level) can inline some scalar functions. 

- See [05c Blueprint Functions Scalar Function Rewrites Demo video 🗗](https://www.erikdarlingdata.com/sql-server/software-vendor-mistakes-with-sql-server-not-using-inline-table-valued-functions/?mc_cid=e0205e6bac&mc_eid=90079896ec#:~:text=In%20the%20videos%20below%2C%20I%E2%80%99m%20going%20to%20show%20you%20how%20to%20rewrite%20T%2DSQL%20Scalar%20User%20Defined%20Functions%2C%20and%20a%20really%20cool%20thing%20they%20can%20do%20on%20top%20of%20just%20replacing%20the%20bad%20kinds%20of%20functions.){:target="_blank" rel="noopener"} (by Erik Darling) to see how to rewrite T-SQL Scalar User Defined Function into a Inline Table Valued function.

Microsoft has been removing (instead of fixing) the inlineablity of scalar functions with every cumulative update. If your query requires scalar functions, you should ensure they are being inlined. Reference: [Inlineable scalar UDFs requirements 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/user-defined-functions/scalar-udf-inlining?view=sql-server-ver15#inlineable-scalar-udfs-requirements){:target="_blank" rel="noopener"} (by Microsoft)

**Run this query to check if your function is inlineable. (SQL Server 2019+ & Azure SQL Server)**

```sql
SELECT
    [Schema]     = SCHEMA_NAME(O.schema_id)
   ,Name         = O.name
   ,FunctionType = O.type_desc
   ,CreatedDate  = O.create_date
FROM
    sys.sql_modules        AS SM
    INNER JOIN sys.objects AS O ON O.object_id = SM.object_id
WHERE
    SM.is_inlineable = 1;
```

**Scalar UDFs typically end up performing poorly due to the following reasons:**

**Iterative invocation:** UDFs are invoked in an iterative manner, once per qualifying tuple. This incurs additional costs of repeated context switching due to function invocation. Especially, UDFs that execute Transact-SQL queries in their definition are severely affected.

**Lack of costing:** During optimization, only relational operators are costed, while scalar operators are not. Prior to the introduction of scalar UDFs, other scalar operators were generally cheap and did not require costing. A small CPU cost added for a scalar operation was enough. There are scenarios where the actual cost is significant, and yet remains underrepresented.

**Interpreted execution:** UDFs are evaluated as a batch of statements, executed statement-by-statement. Each statement itself is compiled, and the compiled plan is cached. Although this caching strategy saves some time as it avoids recompilations, each statement executes in isolation. No cross-statement optimizations are carried out.

**Serial execution:** SQL Server does not allow intra-query parallelism in queries that invoke UDFs.

- See [Refactor SQL Server scalar UDF to inline TVF to improve performance 🗗](https://www.mssqltips.com/sqlservertip/4772/refactor-sql-server-scalar-udf-to-inline-tvf-to-improve-performance/){:target="_blank" rel="noopener"} by Simon Liew

[Back to top](#top)

---

<a name="24"/>

## Using User-Defined Scalar Function
**Check Id:** 24

You should inline your scalar function in SQL query. This means duplicate the code you would put in the scalar function in your SQL code. SQL Server 2019 & Azure SQL Database (150 database compatibility level) can inline some scalar functions. 

Microsoft has been removing (instead of fixing) the inlineablity of scalar functions with every cumulative update. If your query requires scalar functions you should ensure they are being inlined. Reference: [Inlineable scalar UDFs requirements 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/user-defined-functions/scalar-udf-inlining?view=sql-server-ver15#inlineable-scalar-udfs-requirements){:target="_blank" rel="noopener"} by Microsoft

**Run this query to check if your function is inlineable. (SQL Server 2019+ & Azure SQL Server)**

```sql
SELECT
    [Schema]     = SCHEMA_NAME(O.schema_id)
   ,Name         = O.name
   ,FunctionType = O.type_desc
   ,CreatedDate  = O.create_date
FROM
    sys.sql_modules        AS SM
    INNER JOIN sys.objects AS O ON O.object_id = SM.object_id
WHERE
    SM.is_inlineable = 1;
```

**Scalar UDFs typically end up performing poorly due to the following reasons:**

**Iterative invocation:** UDFs are invoked in an iterative manner, once per qualifying tuple. This incurs additional costs of repeated context switching due to function invocation. Especially, UDFs that execute Transact-SQL queries in their definition are severely affected.

**Lack of costing:** During optimization, only relational operators are costed, while scalar operators are not. Prior to the introduction of scalar UDFs, other scalar operators were generally cheap and did not require costing. A small CPU cost added for a scalar operation was enough. There are scenarios where the actual cost is significant, and yet remains underrepresented.

**Interpreted execution:** UDFs are evaluated as a batch of statements, executed statement-by-statement. Each statement itself is compiled, and the compiled plan is cached. Although this caching strategy saves some time as it avoids recompilations, each statement executes in isolation. No cross-statement optimizations are carried out.

**Serial execution:** SQL Server does not allow intra-query parallelism in queries that invoke UDFs.

[Back to top](#top)

---

<a name="19"/>

## Not Using SET NOCOUNT ON in Stored Procedure or Trigger
**Check Id:** 19

Use `SET NOCOUNT ON;` at the beginning of your SQL batches, stored procedures for report output and triggers in production environments, as this suppresses messages like '(10000 row(s) affected)' after executing INSERT, UPDATE, DELETE and SELECT statements. This improves the performance of stored procedures by reducing network traffic.

`SET NOCOUNT ON;` is a procedural level instructions and as such there is no need to include a corresponding `SET NOCOUNT OFF;` command as the last statement in the batch. 

`SET NOCOUNT OFF;` can be helpful when debugging your queries in displaying the number of rows impacted when performing INSERTs, UPDATEs and DELETEs.

```sql
CREATE OR ALTER PROCEDURE dbo.PersonInsert
    @PersonId int
   ,@JobTitle nvarchar(100)
   ,@HiredOn  date
   ,@Gender   char(1)
AS
BEGIN
    SET NOCOUNT ON;

    INSERT INTO
        dbo.Person (PersonId, JobTitle, HiredOn, Gender)
    SELECT 
        PersonId = @PersonId, 
        JobTitle = 'CEO', 
        HiredOn  = '5/2/1971', 
        Gender   = 'M';
END;
```

[Back to top](#top)

---

<a name="15"/>

## Using NOLOCK (READ UNCOMMITTED)
**Check Id:** 15

Using `WITH (NOLOCK)`, `WITH (READUNCOMMITTED)`, or `TRANSACTION ISOLATION LEVEL READ UNCOMMITTED` does not mean your SELECT query does not take out a lock, it does not obey locks.

Can `NOLOCK` be used when the data is not changing? Nope. It has the same problems.

**Problems**
- You can see rows twice
- You can skip rows altogether
- You can see rows that were never committed
- Your query can fail with an error "could not continue scan with `NOLOCK` due to data movement"

These problems will cause non-reproducible errors. You might end up blaming the issue on user error which will not be accurate.

Only use `NOLOCK` when the application stakeholders understand the problems and approve of them occurring. Get their approval in writing to CYA.

Alternatives to `NOCOUNT` is to use index tuning and use READ COMMITTED SNAPSHOT ISOLATION (RCSI).
    - See [Not Using Read Committed Snapshot Isolation](/best-practices-and-findings/environment-conventions#161)

- See [You can minimize locking contention while protecting transactions from dirty reads of uncommitted data modifications by using either of the following 🗗](https://learn.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-ver16#:~:text=You%20can%20minimize%20locking%20contention%20while%20protecting%20transactions%20from%20dirty%20reads%20of%20uncommitted%20data%20modifications%20by%20using%20either%20of%20the%20following){:target="_blank" rel="noopener"} by Microsoft
- See [NOLOCK Is Bad And You Probably Shouldn’t Use It 🗗](https://www.brentozar.com/archive/2021/11/nolock-is-bad-and-you-probably-shouldnt-use-it/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Using NOLOCK? Here’s How You’ll Get the Wrong Query Results 🗗](https://www.brentozar.com/archive/2018/10/using-nolock-heres-how-youll-get-the-wrong-query-results/){:target="_blank" rel="noopener"} by Brent Ozar
- See ["But NOLOCK Is Okay When My Data Isn’t Changing, Right?" 🗗](https://www.brentozar.com/archive/2019/08/but-nolock-is-okay-when-the-data-isnt-changing-right/){:target="_blank" rel="noopener"} by Brent Ozar
- See ["But Surely NOLOCK Is Okay If No One’s Changing Data, Right?" 🗗](https://www.brentozar.com/archive/2021/01/but-surely-nolock-is-okay-if-no-ones-changing-data-right/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Transaction Isolation Levels 🗗](https://learn.microsoft.com/en-us/sql/odbc/reference/develop-app/transaction-isolation-levels){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="124"/>

## Not Using Table Alias
**Check Id:** 124 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Table+Alias)

Use aliases for your table names in most multi-table T-SQL statements; a useful convention is to make the alias out of the first or first two letters of each capitalized table name, e.g., "Phone" becomes "P" and "PhoneType" becomes "PT".

Columns in the `WHERE` and `GROUP BY` sections should also be qualified with its table name. Even if the column name is currently unambiguous, at some point a change to the query could be made that will cause an error.

If you would have duplicate two-character aliases, choose a third letter from each. "PhoneType" becomes "PHT" and "PlaceType" becomes "PLT".

Ensure you use ``AS`` between the table name and the alias like ``[TABLE-Name] AS [ALIAS]``. ``AS`` should be used to make it clear that something is being given a new name and easier to spot. This rule also applies for column aliases if the column aliases is not listed first.

Do not use table aliases like S1, S2, S3. Make your aliases meaningful.

```sql
SELECT
    P.PhoneId
   ,P.PhoneNumber
   ,PT.PhoneTypeName
FROM
    dbo.Phone                AS P
    INNER JOIN dbo.PhoneType AS PT
        ON P.PhoneTypeId = PT.PhoneTypeId;
```

[Back to top](#top)

---

<a name="125"/>

## Not Using Column List For INSERT
**Check Id:** 125 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Column+List+For+INSERT)

Always use a column list in your INSERT statements. This helps in avoiding problems when the table structure changes (like adding or dropping a column). 

```sql
INSERT INTO
    dbo.Person (PersonId, JobTitle, HiredOn, Gender)
SELECT
    PersonId     = 1
   ,JobTitle     = 'CEO'
   ,HiredOn      = '5/2/1971'
   ,Gender       = 'M';
````

[Back to top](#top)

---

<a name="126"/>

## Not Using SQL Formatting
**Check Id:** 126 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+SQL+Formatting)

SQL code statements should be arranged in an easy-to-read manner. When statements are written all on one line or not broken into smaller easy-to-read chunks, it is hard to decipher.

Your SQL code should be formatted in a consistent manner so specific elements like keywords, data types, table names, functions can be identified at a quick glance.

Use one of the two Redgate SQL Prompt formatting styles "[Team Collapsed 🗗](https://github.com/kevinmartintech/sp_Develop/tree/master/Development%20Application%20Settings/Red%20Gate/SQL%20Prompt/Styles){:target="_blank" rel="noopener"}" or "[Team Expanded 🗗](https://github.com/kevinmartintech/sp_Develop/tree/master/Development%20Application%20Settings/Red%20Gate/SQL%20Prompt/Styles){:target="_blank" rel="noopener"}". If you edit T-SQL code that was in a one of the two styles, put the style back to its original style after you completed editing.

- See [Redgate SQL Server Prompt](/development-app-settings#redgate-sql-server-prompt)

[Back to top](#top)

---

<a name="192"/>

## Old-Style TOP Clause is Used
**Check Id:** 192 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Old-Style+TOP+Clause+is+Used)

To limit query results, it's recommended to use the new style TOP clause: `TOP(n)`. This syntax, with parentheses, is clearer and more readable, even though the parentheses are syntactic sugar since only one parameter can be included.

See [TOP (Transact-SQL) 🗗](https://learn.microsoft.com/en-us/sql/t-sql/queries/top-transact-sql){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="127"/>

## Not Using UPPERCASE for Keywords
**Check Id:** 127 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+UPPERCASE+for+Keywords)

Keywords like `SELECT`, `FROM`, `GROUP BY` should be in UPPERCASE. 

- See [Not Using SQL Formatting](/best-practices-and-findings/sql-code-conventions#126)

[Back to top](#top)

---

<a name="128"/>

## Not Converting to Unicode
**Check Id:** 128 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Converting+to+Unicode)

You need to put an ``N`` in from of your strings to ensure the characters are converted to Unicode before being placed into a ```nvarchar``` column.

In the example below FirstName is varchar(100) and LastName is nvarchar(100).

```sql
INSERT INTO dbo.Person (FirstName, LastName) 
VALUES ('Kevin', N'马丁');
```

Not converting to Unicode for ``nvarchar()`` columns will also cause implicit conversions which will make a query non-SARGable in ``WHERE`` clauses.

- See [Using a Non-SARGable Expression in a WHERE Clause](/best-practices-and-findings/sql-code-conventions#100)


[Back to top](#top)

---

<a name="129"/>

## Not Using lower case for Data Types
**Check Id:** 129 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+lower+case+for+Data+Types)

Data types should be lower cased to match the exact case that is in the ``SELECT * FROM sys.types;`` table. This will ensure collation differences won't cause unexpected errors. You should always code defensively and match the case sensitivity of all objects, variables, and parameters.

**Use:** ``DECLARE @Id AS int`` **instead of:** ``DECLARE @Id AS INT``

**Use:**

```sql
CREATE TABLE dbo.Person (
    PersonId  int          IDENTITY(1, 1) NOT NULL
   ,FirstName nvarchar(50) NULL
   ,LastName  varchar(50)  NULL
);
```

**instead of:**

```sql
CREATE TABLE dbo.Person (
    PersonId  INT          IDENTITY(1, 1) NOT NULL
   ,FirstName NVARCHAR(50) NULL
   ,LastName  VARCHAR(50) NULL
);
```

- See [BackToBasics: Why I Use Lower Case for Data Type Names (Now)) 🗗](https://thwack.solarwinds.com/groups/data-driven/b/blog/posts/backtobasics-why-i-use-lower-case-for-data-type-names-now){:target="_blank" rel="noopener"} by Aaron Bertrand

[Back to top](#top)

---

<a name="130"/>

## Set Option Cause Recompile
**Check Id:** 130 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Set+Option+Cause+Recompile)

Setting options in batches, stored procedures, and triggers cause recompilation. They should be compiled just once and have their plans reused for subsequent calls. The query will be more performant and use less memory.

[Back to top](#top)

---

<a name="131"/>

## Using Column Number in ORDER BY
**Check Id:** 131 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Column+Number+in+ORDER+BY)

Use the column name in your ORDER BY instead of the column number. The use of constants in the ORDER BY is deprecated for future removal. It makes it difficult to understand the code at a glance and leads to issue when alter the order of the columns in the SELECT.

```sql
SELECT
     P.FirstName
    ,P.LastName
FROM
    dbo.Person AS P
ORDER BY
    2;

SELECT
     P.FirstName
    ,P.LastName
FROM
    dbo.Person AS P
ORDER BY
    P.LastName ASC;
```

[Back to top](#top)

---

<a name="132"/>

## Commented Out Code
**Check Id:** 132 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Commented+Out+Code)

Remove commented out code.

Commented code hides what's important and it is out of date. Rely on the version control system to keep track of previous code.

- See [Unused Database Objects](/best-practices-and-findings/environment-conventions#181)

[Back to top](#top)

---

<a name="133"/>

## Not Using Location Comment
**Check Id:** 133 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Location+Comments)

When creating dynamic SQL or software originated SQL code, insert a comment explaining where it came from, be it a stored procedure name, method name, function name, some name that will help track down what is generating this SQL Code.

When looking at the SQL Server execution plan cache for dynamic SQL and SQL originating in software code, we cannot see the parent object like we can with non-dynamic database stored procedures or functions. It only reports as ‘Statement’ for Query Type. The SQL command location comment helps a team member or query tuner easier identify the lineage of which piece of code built the SQL command. The location comment can be found in the stored query plan by viewing the query text.

![Query Type Statement vs. Parent](../Images/Not_Using_Location_Comment_Statement_Parent.png)

Use static text for the location comment and do not create dynamic comments. Static comment text allows for a single plan cache to be created and reused.

**Below is good:**

```sql
/* dbo.MyStoredProcedure */ 
SELECT * FROM dbo.Person WHERE FirstName = 'Kevin'
```

It is tempting to add things like the date and time or the username who ran the SQL command. Dynamic comment text will create plan cache pollution having multiple plan caches.

**Below is bad:**

```sql
/* Namespace.MyMethod – ran at 2022-01-28 22:48:12.423 */ 
SELECT * FROM dbo.Person WHERE FirstName = 'Kevin'
```

[Back to top](#top)

---

<a name="134"/>

## Using Double Dash Instead of Block Comment
**Check Id:** 134 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+double+dash+instead+of+block+comments)

Use block comments `/* comment */` instead of double-dash `-- comment` comments in your T-SQL code. Double-dash comments that are copy and pasted when performance tuning makes it difficult to know where the single line comment ends. 

If this is the query and double-dash `-- comment` comment is used:
```sql
SELECT
    *
FROM
    dbo.Person
WHERE
    FirstName = 'Kevin' -- This line is a comment
    AND LastName = 'Martin'
ORDER BY
    LastName ASC;
```

It turns into this in monitoring tools and Dynamic Management Views:
```sql
SELECT * FROM dbo.Person WHERE FirstName = 'Kevin' -- This line is a comment AND LastName = 'Martin' ORDER BY LastName ASC;
```

You cannot tell where the query ends and it will break tools like Redgate SQL Prompt.

The better method is to use the block style comments `/* comment */` like:

```sql
SELECT
    *
FROM 
    dbo.Person
WHERE
    FirstName = 'Kevin' /* This line is a comment */
    AND LastName = 'Martin'
ORDER BY
    LastName;
```

The collapse query below will format correctly in SSMS with Redgate SQL Prompt.

```sql
SELECT * FROM dbo.Person WHERE FirstName = 'Kevin' /* This line is a comment */ AND LastName = 'Martin' ORDER BY LastName;
```

[Back to top](#top)

---

<a name="135"/>

## Not Using Code Comments
**Check Id:** 135 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Code+Comments)

Important code blocks within stored procedures and functions should be commented. Brief functionality descriptions should be included where important or complicated processing is taking place.


Stored procedures and functions should include at a minimum a header comment with a brief overview of the batch's functionality and author information.

You can skip including the Author, Created On & Modified On details when you use source control. (You should be using source control!)

```sql
/**********************************************************************************************************************
** Author:      Your Name
** Created On:  1/22/20??
** Modified On: 8/5/20??
** Description: Description of what the query does goes here. Be specific and don't be afraid to say too much. More is 
                better every single time. Think about "what, when, where, how and why" when authoring a description.
**********************************************************************************************************************/

```

[Back to top](#top)

---

<a name="136"/><a name="not-using-schema"/><a name="not-using-table-schema"/>

## Not Using Fully Qualified Name
**Check Id:** 136 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Table+Schema)

Prefix all database objects like tables, Views, and stored procedures with the schema (in most cases `dbo.`). This results in a performance gain as the optimizer does not have to perform a lookup on execution as well as minimizing ambiguities in your T-SQL.

By including the schema, we avoid certain bugs, minimize the time the engine spends searching for the procedure, and help ensure that cached query plans for the procedures get reused.

Not including the schema on a stored procedure execution will lead to compile locks. For example, the stored procedure named `dbo.ProductGet` that is owned by `dbo`, and another application connection string user named `app_Web` runs this stored procedure by using the command `EXECUTE ProductGet;`, the initial cache lookup by object name fails because the object is not owner-qualified. SQL Server does not yet know whether another stored procedure named `app_Web.ProductGet` exists and SQL Server cannot be sure that the cached execution plan for `dbo.ProductGet` is the correct one to use. SQL Server needs to obtain an exclusive compile lock and prepares to compile. SQL Server performs a precise search of the procedure cache to locate a previously compiled plan even without owner qualification.

Obtaining the lock and performing lookups and other work that is needed to reach this point can introduce a delay for the compile locks that leads to blocking. This is especially true if many users who are not the stored procedure's owner concurrently run the procedure without supplying the owner's name.

Use `EXECUTE dbo.ProductGet;` and not `EXECUTE ProductGet;`.

In most cases, we will want to use 2-part naming conventions like SchemaName.TableName (dbo.Person), or SchemaName.ViewName (app.PersonDetail), or SchemaName.StoreProcedureName (stage.PersonIngest) as a fully qualified name. There are 3- and 4-part naming but are generally not used as best practices or have small use cases.

3-part and higher naming like DatabaseName.SchemaName.TableName breaks having the SQL code be generic enough that it can be deployed to different databases or environments like [AdventureWorks | AdventureWorks Dev | AdventureWorks Test, ...].

For completeness, the 4-part looks like ServerName.DatabaseName.SchemaName.TableName.


- See [Additional Scenarios that lead to compile locks (1. Stored Procedure is executed without Fully Qualified Name) 🗗](https://docs.microsoft.com/en-us/troubleshoot/sql/performance/troubleshoot-blocking-caused-compile-locks#additional-scenarios-that-lead-to-compile-locks:~:text=Stored%20Procedure%20is%20executed%20without%20Fully%20Qualified%20Name){:target="_blank" rel="noopener"} by Microsoft
- See [Using Different Case When Executing Store Procedure](/best-practices-and-findings/sql-code-conventions#164)

[Back to top](#top)

---

<a name="164"/>

## Using Different Case When Executing Store Procedure
**Check Id:** 164 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Different+Case+When+Executing+Store+Procedure)

If an owner-qualified procedure is executed by using a different case (upper or lower) from the case that was used to create it, the procedure can trigger a CacheMiss event or request a COMPILE lock. Eventually, the procedure uses the cached plan and is not recompiled. But the request for a COMPILE lock can sometimes cause a blocking chain situation if there are many SPIDs that are trying to execute the same procedure by using a different case than the case that was used to create it. This is true regardless of the sort order or collation that is being used on the server or on the database. The reason for this behavior is that the algorithm that is being used to find the procedure in cache is based on hash values (for performance), and the hash values can change if the case is different.

The workaround is to drop and create the procedure by using the same case as the one that is used when the application executes the procedure. You can also make sure that the procedure is executed from all applications by using the correct case (upper or lower).

- See [Additional Scenarios that lead to compile locks (3. Stored procedure is invoked using a different case (upper /lower)) 🗗](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/performance/troubleshoot-blocking-caused-compile-locks#:~:text=Stored%20procedure%20is%20invoked%20using%20a%20different%20case%20(upper%20/lower)){:target="_blank" rel="noopener"} by Microsoft
- See [Not Using Fully Qualified Name](/best-practices-and-findings/sql-code-conventions#136)

[Back to top](#top)

---
<a name="23"/>

## Using SELECT *
**Check Id:** 23

Do not use the `SELECT *` in production code unless you have a good reason, instead specify the column names and bring back only those columns you need; this optimizes query performance and eliminates the possibility of unexpected results when columns are added to a table.

`SELECT *` in `IF EXISTS` statements are OK. "*" in math equations is OK.

Reasons not to use `SELECT *`:
- **Unnecessary Input / Output** will need to read more SQL Server 8k pages than required
- **Increased Network Traffic** will take more bandwidth for more data
- **More Application Memory** will need more memory to hold more data
- **Dependency on Order of Columns on Result Set** will mess up the order the columns are returned
- **Breaks Views While Adding New Columns to a Table** the view columns are created at the time of the view creation, you will need to refresh "sp_refreshview()"
- **Copying Data From One Table to Another** your SELECT * INTO table will break with new columns

[Back to top](#top)

---

<a name="9"/>

## Using Hardcoded Database Name Reference
**Check Id:** 9

Use two-part instead three-part names for tables. You should use "dbo.Customer" instead of "DatabaseName.dbo.Customer" in the FROM clause.

It is common to need a database to operate under different names.

- In development, test, production environments
  - This allows the same code to execute if the database names are "CompanyName-Prod" or "CompanyName-Dev"
- When branching database code in source control
  - You might want to name a database after a feature branch on the same SQL Server instance.
- When building database code
  - To validate database objects compiled from a source, it is best to not have the database name hardcoded. If you use a hardcoded name you need to ensure that only one build server can run a build for that database instance.

[Back to top](#top)

---

<a name="137"/>

## Using @@IDENTITY Instead of SCOPE_IDENTITY
**Check Id:** 137 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+@@IDENTITY+Instead+of+SCOPE_IDENTITY)

The generation of an identity value is not transactional, so in some circumstances, `@@IDENTITY` returns the wrong value and not the value from the row you just inserted. This is especially true when using triggers that insert data, depending on when the triggers fire. The `SCOPE_IDENTITY` function is safer because it always relates to the current batch (within the same scope). Also consider using the `IDENT_CURRENT` function, which returns the last identity value regardless of session or scope. The OUTPUT clause is a better and safer way of capturing identity values.

[Back to top](#top)

---

<a name="138"/><a name="using-between-for-datetime-ranges"/>

## Not Accounting for Time in a Range
**Check Id:** 138 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Accounting+for+Time+in+a+Range)

You need to account for the end range being one of the ```datetime``` data types, or you might be excluding rows. 

This can be an exclusion problem for dates with times and utilizing ```BETWEEN```. This can also occur using ```>= ... AND <= ...``` methodology. A day ends at "23:59:59.9999999" or shorter based on the ```datetime``` length, right before the chime of midnight.

```BETWEEN``` is ambiguous. If a restaurant states they are open 9-5, that include all times between 9:00 AM and 5:00 PM. When you score a goal in hockey, the goal only counts when the puck is between the goal posts. In SQL Server, ```BETWEEN``` is a closed interval that includes both ends of the range. Some consider avoiding ```BETWEEN``` for date range queries. Source [Bad Habits to Kick: Mis-handling date / range queries 🗗](
https://sqlblog.org/2009/10/16/bad-habits-to-kick-mis-handling-date-range-queries#:~:text=avoid%20BETWEEN%20for%20range%20queries%3B){:target="_blank" rel="noopener"} (by Aaron Bertrand)

There are two examples below to accomplish this using a subtraction trick. 

**With >= ... AND <= ...**

```sql
USE WideWorldImporters;
GO

UPDATE Sales.Orders SET PickingCompletedWhen = '2013-01-09 23:59:59.9999999' WHERE OrderId = 469 /* This will be included in the results */
UPDATE Sales.Orders SET PickingCompletedWhen = '2013-01-10 00:00:00.0000000' WHERE OrderId = 491 /* This will not be included in the results */

DECLARE
    @PickingCompletedWhenFrom datetime2(7) = '2013-01-09'
   ,@PickingCompletedWhenTo   datetime2(7) = '2013-01-09';

SELECT
    OrderID
   ,PickingCompletedWhen
FROM
    Sales.Orders
WHERE
    PickingCompletedWhen     >= @PickingCompletedWhenFrom
    AND PickingCompletedWhen <= DATEADD(NANOSECOND, -100, DATEADD(DAY, 1, @PickingCompletedWhenTo))
ORDER BY
    PickingCompletedWhen DESC;
```

**With BETWEEN**

```sql
USE WideWorldImporters;
GO

UPDATE Sales.Orders SET PickingCompletedWhen = '2013-01-09 23:59:59.9999999' WHERE OrderId = 469 /* This will be included in the results */
UPDATE Sales.Orders SET PickingCompletedWhen = '2013-01-10 00:00:00.0000000' WHERE OrderId = 491 /* This will not be included in the results */

DECLARE
    @PickingCompletedWhenFrom datetime2(7) = '2013-01-09'
   ,@PickingCompletedWhenTo   datetime2(7) = '2013-01-09';

SELECT
    OrderID
   ,PickingCompletedWhen
FROM
    Sales.Orders
WHERE
    PickingCompletedWhen BETWEEN @PickingCompletedWhenFrom AND DATEADD(NANOSECOND, -100, DATEADD(DAY, 1, @PickingCompletedWhenTo))
ORDER BY
    PickingCompletedWhen DESC;
```

[Back to top](#top)

---

<a name="139"/>

## Using Non-ANSI NOT EQUAL Operator
**Check Id:** 139 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Non-ANSI+NOT+EQUAL+Operator)

Use ANSI-style NOT_EQUAL operator ```<>```.

The ```!=``` symbol for not equal is not part of the SQL language. It is understood but only for the sake of backward-compatibility.

[Back to top](#top)

---

<a name="140"/>

## Using Old Sybase JOIN Syntax
**Check Id:** 140 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Old+Sybase+JOIN+Syntax)

The deprecated syntax (which includes defining the join condition in the WHERE clause) is not standard SQL and is more difficult to inspect and maintain. Parts of this syntax are completely unsupported in SQL Server 2012 or higher.

The "old style" Microsoft/Sybase JOIN style for T-SQL, which uses the =* and *= syntax, has been deprecated and is no longer used. Queries that use this syntax will fail when the database engine level is 10 (SQL Server 2008) or later (compatibility level 100).

- See [Finding code smells using SQL Prompt: old-style join syntax (ST001) 🗗](https://www.red-gate.com/hub/product-learning/sql-prompt/finding-code-smells-using-sql-prompt-old-style-join-syntax-st001){:target="_blank" rel="noopener"} by Phil Factor at Redgate
- See [Not Specifying JOIN Type](/best-practices-and-findings/sql-code-conventions#141)

[Back to top](#top)

---

<a name="141"/>

## Not Specifying JOIN Type
**Check Id:** 141 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Specifying+JOIN+Type)

It is always better to specify the type of join you require, INNER JOIN, LEFT OUTER JOIN (LEFT JOIN), RIGHT OUTER JOIN (RIGHT JOIN), FULL OUTER JOIN (FULL JOIN) and CROSS JOIN, which has been standard since ANSI SQL-92 was published.

While you can choose any supported JOIN style, without affecting the query plan used by SQL Server, using the ANSI-standard syntax will make your code easier to understand, more consistent, and portable to other relational database systems.

[Back to top](#top)

---

<a name="142"/>

## Using LEFT OUTER JOIN Instead of INNER JOIN
**Check Id:** 142 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+LEFT+OUTER+JOIN+Instead+of+INNER+JOIN)

Use ```INNER JOIN``` when rows will match in both tables. Rows will match in both tables if the foreign key column does not allow ```NULL```.

[Back to top](#top)

---

<a name="143"/>

## Using INNER JOIN Instead of LEFT OUTER JOIN
**Check Id:** 143 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+INNER+JOIN+Instead+of+LEFT+OUTER+JOIN)

Use ```LEFT OUTER JOIN``` when rows might not always match in both tables. Rows might not match in both tables if the foreign key column allows ```NULL```.

[Back to top](#top)

---

<a name="188"/>

## LEFT JOIN with WHERE Clause 
**Check Id:** 188 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=LEFT+JOIN+with+WHERE+Clause)

Using a `LEFT JOIN` means returning all rows from the left table and matching rows from the right table, even if no matches exist. However, placing conditions on the right-hand table within the `WHERE` clause can turn the `LEFT JOIN` into an `INNER JOIN` by filtering out `NULL` values, which defeats the purpose of the `LEFT JOIN`.

Place any conditions for the right-hand table in the `ON` clause.

Do This:

```sql
SELECT
    C.CustomerName
   ,O.OrderId
FROM
    dbo.Customer    AS C
LEFT JOIN dbo.Order AS O ON C.CustomerId = O.CustomerId
                          AND O.OrderDate = '2024-09-01'; /* ← Look here */
```

Not This:

```sql
SELECT
    C.CustomerName
   ,O.OrderID
FROM
    dbo.Customer    AS C
LEFT JOIN dbo.Order AS O ON C.CustomerId = O.CustomerId
WHERE
    O.OrderDate = '2024-09-01'; /* ← Look here */
```

- See [SQL Tip: LEFT JOINs and WHERE clauses…are they really LEFT JOINs? 🗗](https://sqlbenjamin.wordpress.com/2017/12/23/sql-tip-left-joins-and-where-clauses-are-they-really-left-joins){:target="_blank" rel="noopener"} by Benjamin's Blog
- See [Left Join With Where Clause 🗗](https://stackoverflow.com/questions/4752455/left-join-with-where-clause){:target="_blank" rel="noopener"} on Stack Overflow

[Back to top](#top)

---

<a name="144"/>

## Order of Columns in JOIN Clause
**Potential Finding:** <a name="backward-order-of-columns-in-join-clause"/>Backward Order of Columns in JOIN Clause<br/>
**Check Id:** 144 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Backward+Order+of+Columns+in+JOIN+Clause)

I recommends the [Microsoft Docs 🗗](https://learn.microsoft.com/en-us/sql/relational-databases/performance/joins?view=sql-server-ver16#:~:text=The%20following%20is%20a%20simple%20SELECT%20statement%20using%20this%20join){:target="_blank" rel="noopener"} column order in JOIN clauses for consistency noted in the "Do This" code below.

It is always recommended to adapt the naming and coding conventions of inherited projects.

**There is a setting for this in Redgate SQL Prompt:** Click the 'SQL Prompt' navigation menu > Click 'Join conditions' in the 'Suggestions' section > Check/tick the 'Swap order of columns in join clauses' checkbox in the 'Column order' section > Click the 'OK' button.

In the example below, the StateProvince table is joined to the CountryRegion table. StateProvince -> CountryRegion. 

### Do This:

```sql
SELECT
    SP.StateProvinceCode
FROM
    dbo.StateProvince            AS SP
    INNER JOIN dbo.CountryRegion AS CR
        ON SP.CountryRegionId = CR.CountryRegionId; /* ← Look here */
```

### Not This:
```sql
SELECT
    SP.StateProvinceCode
FROM
    dbo.StateProvince            AS SP
    INNER JOIN dbo.CountryRegion AS CR
        ON CR.CountryRegionId = SP.CountryRegionId; /* ← Look here */
```

[Back to top](#top)

---

<a name="145"/>

## View Usage
**Check Id:** 145 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=View+Usage)

Ask yourself what you are gaining by creating a view.

Views do not lend themselves to being deeply nested. Views that reference views are difficult to maintain.

A view can be helpful with the use cases below and should be no less performant, it matters what you put in the view that will give you problems.

- **View Use Cases**
  - Create a temporary indexed view for performance issues you cannot solve without changing T-SQL code
  - You need to retire a table and use a new table with similar data (still should be a temporary use)
  - For security reasons to expose only specific data to a database role
  - As an interface layer for a client that does not support a table or stored procedure data source
  - Abstracting complicated base tables
  - Data aggregation like `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()`, ...
    - It is best to include this aggregation code in a stored procedure, but if there is a need to not have duplicated business logic code in multiple stored procedures, these data aggregation views can be helpful.
      - If a view does not work and you need parameters, try an Inline Table Valued Function (iTVF). Views and iTVF at runtime are both inlined and treaded similarly to derived tables or CTEs.
    - These aggregation views can be used to `JOIN` on with single row data like an Event. The aggregations would be used to sum up the Attendees joined on the EventId.
    - Immutable (unchanging) rows can have the parent table's column value set inside a transaction with the child rows.

[Back to top](#top)

---

<a name="146"/>

## Invalid Objects
**Check Id:** 146 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Invalid+Objects)

This check found objects that were deleted, renamed. Use can also run "Find Invalid Objects" with Redgate SQL Prompt in SQL Server Management Studio.

Try running EXEC sp_refreshsqlmodule or sp_refreshview.

[Back to top](#top)

---

<a name="159"/>

## JSON Explicit Schema
**Check Id:** 159 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=JSON+Explicit+Schema)

Explicitly defining the schema JSON columns using [with_clause 🗗](https://learn.microsoft.com/en-us/sql/t-sql/functions/openjson-transact-sql?view=sql-server-ver16#with_clause){:target="_blank" rel="noopener"} (by Microsoft) is more performant.

If you are required to use JSON string in the relational database and need to frequent parse the JSON string, you could create a computed column using `JSON_VALUE(expression, path)` and create an index.

- See [JSON_VALUE (Transact-SQL) 🗗](https://learn.microsoft.com/en-us/sql/t-sql/functions/json-value-transact-sql?view=sql-server-ver16){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="160"/>

## JSON Performance
**Check Id:** 160 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=JSON+Performance)

If you are required to use JSON strings in the relational database and need to frequent parse the JSON string, create a computed column on the table using `JSON_VALUE(expression, path)` and create an index.

```sql
ALTER TABLE dbo.DeliveryInventory ADD MakeName AS JSON_VALUE(JSONAttributes, '$.Make');
CREATE NONCLUSTERED INDEX DealerInventory_MakeName ON dbo.DeliveryInventory (MakeName ASC);
```

- See [JSON_VALUE (Transact-SQL) 🗗](https://learn.microsoft.com/en-us/sql/t-sql/functions/json-value-transact-sql?view=sql-server-ver16){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="165"/>

## Using Inefficient Function
**Check Id:** 165 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+Inefficient+Function)

You are using a function like `FORMAT()` that is extremely inefficient compared to other approaches. Most formatting should be performed in the client and not in the database engine.

- See [FORMAT() is nice and all, but… 🗗](https://sqlperformance.com/2015/06/t-sql-queries/format-is-nice-and-all-but){:target="_blank" rel="noopener"} by Aaron Bertrand
- See [FORMAT is a convenient but expensive SQL Server function - Part 1 🗗](https://www.mssqltips.com/sqlservertip/7144/sql-date-format-comparison-sql-format-sql-convert/){:target="_blank" rel="noopener"} bt Aaron Bertrand
- See [FORMAT is a convenient but expensive SQL Server function – Part 2 🗗](https://www.mssqltips.com/sqlservertip/7145/sql-date-format-comparison-computed-column-materialized-column/){:target="_blank" rel="noopener"} by Aaron Bertrand

[Back to top](#top)

---

<a name="171"/>

## Using RBAR aka Row By Agonizing Row
**Check Id:** 171 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+RBAR+aka+Row+By+Agonizing+Row)

RBAR, which stands for "Row By Agonizing Row," is an anti-pattern where database operations are performed row by row, rather than using set-based operations. This leads to inefficient and slow performance, especially when dealing with large datasets. Other issues are extra network round-trips, increased locking and blocking, code complexity, inefficient use of resources, and difficulty in scaling.

Use set-based operations whenever possible. The SQL language is optimized for set-based operations and developing queries that work on sets of data will take advantage of the database engine's capabilities and be more performant.

Performing one API insert at a time instead of inserting multiple rows with a serialized JSON object parameter in a single call can have several drawbacks including performance overhead resulting in higher network overhead and increased latency, increase database load where each single API call triggers a separate database transaction that leads to increased load impacting performance, atomicity and consistence issues that could prevent partial data commits where you wanted all-or-nothing, higher resource utilization decreasing resource for other database operations, higher risk of blocking and locking. 

Use the [@GenerateCreateMulple 🗗](https://github.com/kevinmartintech/sp_CRUDGen#:~:text=%40GenerateCreateMultiple){:target="_blank" rel="noopener"} parameter in [sp_GRUDGen 🗗](https://github.com/kevinmartintech/sp_CRUDGen){:target="_blank" rel="noopener"} to generate a stored procedure that allows you to serialize and pass in a JSON string parameter to insert multiple rows at once.

- See [The 'Secret' to High Performance Code 🗗](https://www.red-gate.com/simple-talk/databases/sql-server/t-sql-programming-sql-server/rbar-row-by-agonizing-row/){:target="_blank" rel="noopener"} by Redgate
- See [Hidden RBAR: Triangular Joins 🗗](https://www.sqlservercentral.com/articles/hidden-rbar-triangular-joins){:target="_blank" rel="noopener"} by Jeff Moden
- See [Cursor Examples for SQL Server 🗗](https://www.brentozar.com/sql-syntax-examples/cursor-example/){:target="_blank" rel="noopener"} by Brent Ozar


[Back to top](#top)

---

<a name="174"/>

## Causing Lock Escalation With Large Operations
**Check Id:** 174 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Causing Lock+Escalation+With+Large+Operations)

Batching large operations like `INSERT`, `UPDATE`, `DELETE` in SQL Server is crucial to prevent lock escalation. Lock esclation is a process where low-level locks like row or page locks are converted into higher-level table lock, leading to contention and decreased performance. By breaking down large updates into smaller batches, lock contention is minimized, reducing the likelihood of lock escalation. This approach also decreases the duration of locks on the affected data, improving overall concurrency.

You need to pay special attention to batch operations to ensure they do not gradually get slower as it takes longer and longer to scan an index to find rows to delete.


- See [Don't Bite Off More You Can Chew - Take It in Chunks (video) 🗗](https://www.youtube.com/watch?v=oDY6Tlum5tc){:target="_blank" rel="noopener"} by Erland Sommarskog
- See [Take Care When Scripting Batches 🗗](https://michaeljswart.com/2014/09/take-care-when-scripting-batches){:target="_blank" rel="noopener"} by Michael J. Swart
- See [Batching Follow-Up 🗗](https://michaeljswart.com/2022/09/batching-follow-up){:target="_blank" rel="noopener"} by Michael J. Swart
- See [Break large delete operations into chunks 🗗](https://sqlperformance.com/2013/03/io-subsystem/chunk-deletes){:target="_blank" rel="noopener"} by Aaron Bertrand
- See [3.3 Using Batches to Do a Lot of Work Without Blocking 🗗](https://www.brentozar.com/training/mastering-query-tuning-live-3-days-recording/using-batches-to-do-a-lot-of-work-without-blocking/){:target="_blank" rel="noopener"} by Brent Ozar - Mastering Query Tuning Training
- See [How to Batch Updates A Few Thousand Rows at a Time 🗗](https://www.brentozar.com/archive/2020/12/how-to-batch-updates-a-few-thousand-rows-at-a-time/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Resolve blocking problems caused by lock escalation in SQL Server - Prevent lock esclation 🗗](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/performance/resolve-blocking-problems-caused-lock-escalation#prevent-lock-escalation){:target="_blank" rel="noopener"} by Microsoft


[Back to top](#top)

---

<a name="177"/>

## Use TRUNCATE Instead of DELETE
**Check Id:** 177 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Use+TRUNCATE+Instead+of+DELETE)

When possible, you should use `TRUNCATE` on a table instead of `DELETE` when you need to remove all the table rows and have fewer locks are welcomed with less transaction log space used. `TRUNCATE` is an quick, minimally logged and resource-efficient method to clear the table without the overhead of individual row deletion.

- See [TRUNCATE TABLE (Transact-SQL) 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/truncate-table-transact-sql){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="179"/>

## Using a Non-Scalar Subquery to Set a Variable
**Check Id:** 179 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+a+Non-Scalar+Subquery+to+Set+a+Variable)

Do not attempt to set a subquery that returns more than 1 row to a variable.

{: .note }
> [A subquery in the column list must be scalar, meaning that it can return just one value. Even if you correctly place just one expression in your select list, you must also ensure that just one row is returned. TOP (1) can be used if there is an ORDER BY clause](https://documentation.red-gate.com/codeanalysis/code-analysis-for-sql-server/execution-rules/ei003){:target="_blank" rel="noopener"} by Redgate

- See [When to use SET vs SELECT when assigning values to variables in SQL Server 🗗](https://www.mssqltips.com/sqlservertip/1888/when-to-use-set-vs-select-when-assigning-values-to-variables-in-sql-server/){:target="_blank" rel="noopener"} by MSSQLTips
- See [Variables Usage](/best-practices-and-findings/sql-code-conventions#180)

[Back to top](#top)

---

<a name="180"/>

## Variables Usage
**Check Id:** 180 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Variables+Usage)

A local variable produces the same behavior as the `OPTIMIZE FOR UNKNOWN` hint and cannot use the statistics information efficiently and can impact overall performance. This also occurs when a store procedure parameter value is copied to a local variable.

- See [Yet Another Post About Local Variables In SQL Server 🗗](https://erikdarling.com/yet-another-post-about-local-variables/){:target="_blank" rel="noopener"} by Erik Darling
- See [Impact of SQL Variables on Performance 🗗](https://www.sqlshack.com/impact-of-sql-variables-on-performance){:target="_blank" rel="noopener"} by SQLShack (Esat Erkec)
- See [Parameterization Part 5: Two Common Mistakes 🗗](https://www.sqlservercentral.com/blogs/parameterization-part-5-two-common-mistakes){:target="_blank" rel="noopener"} by SQLServerCentral (Guy Glantser)

[Back to top](#top)

---

<a name="195"/>

## Changing Parameter Value Inside Procedure
**Check Id:** 195 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Changing+Parameter+Value+Inside+Procedure)

Changing a stored procedure parameter value from its originally passed in value can lead to plan caching issues and unpredictable performance. SQL Server may reuse a plan optimized for a different parameter value, resulting in suboptimal execution.

- See [Parameterization Part 5: Two Common Mistakes 🗗](https://www.sqlservercentral.com/blogs/parameterization-part-5-two-common-mistakes){:target="_blank" rel="noopener"} by SQLServerCentral (Guy Glantser)
- See [Variables Usage](/best-practices-and-findings/sql-code-conventions#180)

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