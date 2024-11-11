---
title: Environment Conventions
permalink: best-practices-and-findings/environment-conventions
parent: Best Practices & Findings
nav_order: 6
layout: default
---

# Environment Conventions
{: .no_toc }
These checks are for environment and configurations conventions to the SQL Server.

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

<a name="54"/><a name="use-code-retry-logic-to-handle-transient-errors"/>

## Not Using Code Retry Logic
**Check Id:** 54 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Use+Code+Retry+Logic)

It is best practice to implement client code to mitigate connection errors, transient errors, and command errors like deadlocks that your client application encounters when communicating with a SQL Server (On-premises SQL Server, Azure SQL Database, Azure SQL Managed Instance and Azure Synapse Analytics).

Retry logic should be implemented by the application/client code "[but if your stored procedure isn't being called by an application, doing it in T-SQL isn't a horrible alternative 🗗](https://erikdarling.com/the-art-of-the-sql-server-stored-procedure-error-handling/#:~:text=But%20if%20your%20stored%20procedure%20isn%E2%80%99t%20being%20called%20by%20an%20application%2C%20doing%20it%20in%20T%2DSQL%20isn%E2%80%99t%20a%20horrible%20alternative){:target="_blank" rel="noopener"}" - Erik Darling

SQL Server might be in the process of shifting hardware resources  to better load-balance or fail over in a HA/DR (High Availability / Disaster Recover). When this occurs there will be a time normally 60 seconds where your app might have issues with connecting to the database.

Applications that connect to a SQL Server should be built to expect these transient errors. To handle them, implement retry logic in their code instead of surfacing them to users as application errors.

.NET 4.6.1 or later (or .NET Core) can use the [.NET SqlConnection parameters for connection retry 🗗](https://docs.microsoft.com/en-us/azure/azure-sql/database/troubleshoot-common-connectivity-issues#net-sqlconnection-parameters-for-connection-retry){:target="_blank" rel="noopener"} (by Microsoft). 

Ensure you are using the failover group name or availability group listener name in your connection string. The SQL Server name should not be something like 'SQL01'. This indicates you are connecting directly to a specific SQL Server instance instead of a group of SQL Servers.


- See [Implementing Connection Resiliency with Entity Framework 6 🗗](https://www.codeproject.com/Tips/758469/Implementing-Connection-Resiliency-with-Entity-Fra){:target="_blank" rel="noopener"} by CodeProject
- See [Troubleshoot transient connection errors in SQL Database and SQL Managed Instance 🗗](https://docs.microsoft.com/en-us/azure/azure-sql/database/troubleshoot-common-connectivity-issues){:target="_blank" rel="noopener"} by Microsoft
- See [Looking at Entity Framework 6 Execution Strategies, Specifically SqlAzureExecutionStrategy 🗗](https://www.nikouusitalo.com/blog/using-different-execution-strategies/){:target="_blank" rel="noopener"} by Niko Uusitalo
- See [The Art Of The SQL Server Stored Procedure: Error Handling 🗗](https://erikdarling.com/the-art-of-the-sql-server-stored-procedure-error-handling/){:target="_blank" rel="noopener"} by Erik Darling

[Back to top](#top)

---

<a name="161"/>

## Not Using Read Committed Snapshot Isolation
**Check Id:** 161 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Read+Committed+Snapshot+Isolation)

Enabling Read Committed Snapshot Isolation (RCSI) can effectively resolve numerous blocking and deadlocking issues that may arise.

Frequently, the `NOLOCK (READ UNCOMMITTED)` isolation level is employed to mitigate common instances of blocking and locking. However, it is important to note that this approach introduces its own set of challenges associated with dirty reads.

When RCSI is enabled, data read queries no longer block data writer queries, and likewise, data writers do not block the progress of readers.

Read Committed Snapshot Isolation is the default isolation level for Azure SQL databases.

Special attention should be made to ensure your SQL Server can handle the versioning for data modifications in the tempdb or local Accelerated Database Recovery (ADR) in SQL Server 2019 and greater. A healthy SQL Server configured with best practices first is recommended. Lower environment testing or enable allow snapshot isolation and monitoring the workload, then enable read committed snapshot is recommended.

Remember to remove the `NOLOCK` hints.

- See [Using NOLOCK (READ UNCOMMITTED)](/best-practices-and-findings/sql-code-conventions#15)
- See [Implementing Snapshot or Read Committed Snapshot Isolation in SQL Server: A Guide 🗗](https://www.brentozar.com/archive/2013/01/implementing-snapshot-or-read-committed-snapshot-isolation-in-sql-server-a-guide/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Snapshot Isolation in SQL Server 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server){:target="_blank" rel="noopener"} by Microsoft


[Back to top](#top)

---

<a name="55"/><a name="do-not-grant-an-application-user-the-db_owner-role"/>

## Application User Granted db_owner Role
**Check Id:** 55 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Do+Not+Grant+an+Application+User+the+db_owner+Role)

You will want to give an account or process only those privileges which are essential to perform its intended function. Start your development with the app user account only a member of the db_reader, db_writer and db_executor roles.

When a vulnerability is found in the code, service or operating system the "Principle of least privilege" lessens the blast radius of damage caused by hackers and malware.

- See [Principle of least privilege 🗗](https://en.wikipedia.org/wiki/Principle_of_least_privilege){:target="_blank" rel="noopener"} by Wikipedia

[Back to top](#top)

---

<a name="56"/><a name="use-the-query-execution-defaults"/>

## Not Using Query Execution Defaults
**Check Id:** 56 [Not implemented yet. Click here to add the issue if you want to develop and create a pull request.](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Query+Execution+Defaults)

There are query execution defaults included in SSMS (SQL Server Management Studio) and Visual Studio. These defaults must be maintained or overridden at the connection or session level if needed. If the defaults are not consistently used certain TSQL script, stored procedures or functions might not behave as developed.

When dealing with indexes on computed columns and indexed views, four of these defaults (```ANSI_NULLS```, ```ANSI_PADDING```, ```ANSI_WARNINGS```, and ```QUOTED_IDENTIFIER```) must be set to ``ON``. These defaults are among seven ```SET``` options that must be assigned the required values when you are creating and changing indexes on computed columns and indexed views.

The other three ```SET``` options are ```ARITHABORT (ON)```, ```CONCAT_NULL_YIELDS_NULL (ON)```, and ```NUMERIC_ROUNDABORT (OFF)```. For more information about the required ```SET``` option settings with indexed views and indexes on computed columns, see [Considerations When You Use the SET Statement 🗗](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-statements-transact-sql#considerations-when-you-use-the-set-statements){:target="_blank" rel="noopener"} (by Microsoft).

It is not best practice to modify these query execution settings at the SQL Server level.

### SSMS (SQL Server Management Studio) and Visual Studio Settings

In SSMS (SQL Server Management Studio) and Visual Studio these 5 ANSI execution settings are on by default: QUOTED_IDENTIFIER, ANSI_PADDING, ANSI_WARNINGS, ANSI_NULLS, ANSI_NULL_DFLT_ON

These 2 advanced execution settings are on by default: ARITHABORT, CONCAT_NULL_YIELDS_NULL

### Visual Studio Database Projects

Visual Studio database projects should be setup with the 7 query execution SET defaults (Project Settings > ‘Database Settings’ button). If there have been publish database objects without these query execution defaults, they will need to be updated. It is possible to check the “Ignore quoted identifiers” and “Ignore ANSI Nulls” under the ‘Advanced’ button when manually publishing the database project.

- See [SET Statements 🗗](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-statements-transact-sql){:target="_blank" rel="noopener"} by Microsoft
- See [SET ANSI_DEFAULTS (Transact-SQL) 🗗](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-ansi-defaults-transact-sql){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="57"/><a name="the-application-user-should-be-a-contained-user"/>

## Application User is not a Contained User
**Check Id:** 57 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=The+Application+User+should+be+a+Contained+User)

Users that only access one database should generally be created as contained users which means they don't have a SQL Server "login" and are not found as users in the master database. This makes the database portable by not requiring a link to a SQL Server Login. A database with contained users can be restored to your development SQL Server or a migration event needs to occur in production to a different SQL Server.

- See [Contained Database Users - Making Your Database Portable 🗗](https://docs.microsoft.com/en-us/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=sql-server-ver15){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="58"/><a name="all-database-objects-should-be-owned-by-dbo"/>

## Object Not Owned by dbo
**Check Id:** 58 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=All+Database+Objects+Should+be+Owned+by+dbo)

It simplifies object management with dbo owning all the database objects. You will need to transfer ownership of objects before an account can be deleted.

[Back to top](#top)

---

<a name="59"/><a name="the-database-compatibility-level-should-match-the-sql-server-version"/>

## Database Compatibility Level is Lower Than the SQL Server
**Check Id:** 59 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=The+Database+Compatibility+Level+Should+Match+the+SQL+Server+Version)

The database compatibility level lower than the SQL Server it is running on.

There might be query optimization you are not getting to run on an older database compatibility level. You might also introduce issues with a more modern database compatibility level.

[Back to top](#top)

---


<a name="190"/>

## Not Using a Relational Database for Relational Data
**Check Id:** 190 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+a+Relational+Database+for+Relational+Data)

It is a best practice to use a relational database management system (RDBMS) when handling data with complex relationships, such as customers, accounts, people, products, invoices, purchase orders, ... While NoSQL (non-relational) document databases are powerful tools for unstructured big data, they are not ideally suited for managing relational data due to their schema-less nature and lack of enforced data constraints.

**Issues with Using NoSQL Document Databases for Relational Data:**

- **Lack of Schema Enforcement:**
  - NoSQL databases operate on a "schema-on-read" model, which means they do not enforce a schema when data is written.
  - This can lead to inconsistent or invalid data being stored, as there are no constraints to prevent incorrect data entry.
  - Developers may spend additional time implementing workarounds for features natively supported by relational databases.

- **No Constraints on Write:**
  - Absence of foreign keys, primary keys, and unique constraints increases the risk of data anomalies.
  - Without these constraints, maintaining data integrity becomes the responsibility of the application code, which can be error-prone.

- **Data Integrity Challenges:**
  - Relationships between entities must be managed manually, often leading to duplicated or denormalized data.
  - Updating related data across multiple documents can become complex and may result in inconsistencies.

- **Complexity in Querying Relational Data:**
  - Performing joins or complex queries across multiple collections is not as efficient or straightforward as in relational databases.
  - This can lead to performance issues and increased development time for writing and maintaining queries.

**Recommendation:**

- **Use Relational Databases for Relational Data:**
  - Leverage the strengths of RDBMS like enforcing schemas, constraints, and supporting complex joins.
  - This ensures data integrity, consistency, and reduces the risk of errors.

- **Educate Team Members:**
  - Clarify misconceptions about "document databases" being suited for handling document outputs.
  - Emphasize that the term refers to the data storage model, not the type of data (e.g., printed documents or emails).

[Back to top](#top)

---
















<a name="158"/>

## Connection String Not Scalable
**Check Id:** 158 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Connection+String+Not+Scalable)

Application connection strings should be set up to be scalable. 3 different connection strings are recommended.

In the beginning, all three connection strings below will have the same content – they will all point to your production database server. When you need to scale, though, the production DBA can use different techniques to scale out each of those tiers.

1. **Writes with Real-Time Reads**
   - This connection is hard to scale, so keep the number of queries here to a minimum.  
   - Treat this like a valuable resource.
   - Functions like inserting or modifing database rows, then redirecting or displaying the data directly afterwards would fall into the use case pattern. Determine if it is necessary to redirect to a display UI, or just notify the user of the action. In high usage databases, performing additional database queries could consume resources unnecessarly.
   - Indicate on the connection string `ApplicationIntent=ReadWrite`.
     - See [Specifying Application Intent 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#specifying-application-intent){:target="_blank" rel="noopener"} by Microsoft
   - Determine the `MultiSubnetFailover` property of `True` or `False`
     - See [Connecting With MultiSubnetFailover 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover){:target="_blank" rel="noopener"} by Microsoft
2. **No Writes with Reads That Can Tolerate Data Older Than 15 Seconds**
   - This connection string has more options to scale out. This is where the majority of queries should go.
   - Think of this as the default connection string.
   - Indicate on the connection string `ApplicationIntent=ReadOnly`.
     - See [Specifying Application Intent 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#specifying-application-intent){:target="_blank" rel="noopener"} by Microsoft
   - Determine the `MultiSubnetFailover` property of `True` or `False`
     - See [Connecting With MultiSubnetFailover 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover){:target="_blank" rel="noopener"} by Microsoft
3. **No Writes with Reads That Can Tolerate Data Older Than Several Hours**
   - This connection is for operational reporting, and not to be confused with analytical reporting aggregations like `SUM()`, `COUNT()`, `AVG()`, ... reporting. True analytical reporting should be performed in a system like a data warehouse or Online Analytical Arocessing (OLAP) system.
   - Queries of these types utilize higher amounts of CPU and storage Input/Output.
   - Stakeholders will eventually have to decide whether to prioritize near-real-time data for reports at the expense of slowing down production, or to separate these resource-intensive queries to a data source with a longer delay.
   - Indicate on the connection string `ApplicationIntent=ReadOnly`.
     - See [Specifying Application Intent 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#specifying-application-intent){:target="_blank" rel="noopener"} by Microsoft
   - Determine the `MultiSubnetFailover` property of `True` or `False`
     - See [Connecting With MultiSubnetFailover 🗗](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover){:target="_blank" rel="noopener"} by Microsoft


- See [Not Using Code Retry Logic for Transient Errors](/best-practices-and-findings/configuration-issues#54)

[Back to top](#top)

---
<a name="182"/>

## Connection String Settings
**Check Id:** 182 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Connection+String+Settings)

### Application Name for SQL Server Connections

Always include the optional Application Name parameter in your connection strings when connecting to SQL Server. This way the SQL Server will have info on what application is using the connection. This can be invaluable info when locating errors in the database server. Documentation should be kept on how to contact the app developer or include a distribution list email address in the connection strings "Application Name". You can use up to 128 characters for the application name.

Simply include "Application Name=MyAppName &lt;MyAppGroupName@domain.com&gt;;" in the connection string.

- See [Application Name for SQL Server Connections 🗗](https://www.connectionstrings.com/use-application-name-sql-server/){:target="_blank" rel="noopener"} by ConnectionStrings.com

[Back to top](#top)

<a name="164"/><a name="the-database-compatibility-level-should-match-the-sql-server-version"/>

## Using Service Broker or Database as Queue
**Check Id:** 164 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using Service+Broker+or+Database+as+Queue)

Use a different message queue based system other than SQL Server Service Broker.

Using a database as message queue platform is an anti-pattern. You need to poll which hammers the database. Using a single table for inserts, updates and queries are not performant when all three need to occur on the same table. Clearing the records once the workflow is complete, so do you perform and status update on the row or perform a delete which can be inefficient.

- See [Why a database is not always the right tool for a queue based system 🗗](https://www.cloudamqp.com/blog/why-is-a-database-not-the-right-tool-for-a-queue-based-system.html){:target="_blank" rel="noopener"} by Lovisa Johansson
- See [Databases suck for Messaging 🗗](https://www.rabbitmq.com/resources/RabbitMQ_Oxford_Geek_Night.pdf){:target="_blank" rel="noopener"} by Alexis Richardson
- See [The Database As Queue Anti-Pattern 🗗](http://mikehadlow.blogspot.com/2012/04/database-as-queue-anti-pattern.html){:target="_blank" rel="noopener"} by Mike Hadlow at Code Rant
- See [RabbitMQ - the most widely deployed open source message broker 🗗](https://www.rabbitmq.com/){:target="_blank" rel="noopener"} by RabbitMQ
- See [Storage queues and Service Bus queues - compared and contrasted 🗗](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="166"/>

## Slow Network Queries
**Check Id:** 166 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using Service+Broker+or+Database+as+Queue)

There are a couple causes for this type of issue we can see. 

- The first cause is the large result sets, where client apps request numerous rows, leading to unnecessary network utilization and client processing.
    - The recommended resolution is to balance processing between SQL Server and client apps, letting SQL Server handle filtering and aggregations to limit the result at the client app, while more data-related computations should be performed on the client side. Web/app servers cost less than SQL Server's per core license.
- Another cause is when the client app is slow to fetch results and notify SQL Server, resulting in the "ASYNC_NETWORK_IO" wait.
    - The solution is to fetch the results as quickly as possible using a tight loop or storing results in memory for subsequent processing. 
- Another cause might be the client machines under stress, with I/O, memory, or CPU resource constraints, can cause slow processing.
    - The resolution involves diagnosing and eliminating these resource constraints through tools like Performance Monitor.


- See [Troubleshoot slow queries that result from ASYNC_NETWORK_IO wait type 🗗](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/performance/troubleshoot-query-async-network-io){:target="_blank" rel="noopener"} by Microsoft
- See [Reducing SQL Server ASYNC_NETWORK_IO wait type 🗗](https://www.sqlshack.com/reducing-sql-server-async_network_io-wait-type/){:target="_blank" rel="noopener"} by Nikola Dimitrijevic at SQLShack.com

[Back to top](#top)

---

<a name="169"/>

## Not Following the Architecting Microsoft SQL Server on VMware vSphere Best Practices
**Check Id:** 169 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Following+the+Architecting+Microsoft+SQL+Server+on+VMware+vSphere+Best+Practices)

The [Architecting Microsoft SQL Server on VMware vSphere 🗗](https://core.vmware.com/resource/architecting-microsoft-sql-server-vmware-vsphere){:target="_blank" rel="noopener"} by VMware is a best practices guidance for designing and implementing Microsoft SQL Server in a virtual machine to run on VMware vSphere.

For a quick video ramp up on configuring VMware for SQL Server see the [SQL Server on VMware vSphere Accelerator Video Series 🗗](https://www.youtube.com/playlist?list=PLhRuY1yuVB7xBHKjFuTDJthZ456vF4DiW){:target="_blank" rel="noopener"}  by David Klee

- See [Architecting Microsoft SQL Server on VMware vSphere 🗗](https://core.vmware.com/resource/architecting-microsoft-sql-server-vmware-vsphere){:target="_blank" rel="noopener"} by VMware
- See [SQL Server on VMware vSphere Accelerator Video Series 🗗](https://www.youtube.com/playlist?list=PLhRuY1yuVB7xBHKjFuTDJthZ456vF4DiW){:target="_blank" rel="noopener"} by David Klee
- See [Why SQL Server DBAs Need Access to VMware vCenter 🗗](https://www.davidklee.net/articles/sql-server-articles/why-sql-server-dbas-need-access-to-vmware-vcenter/){:target="_blank" rel="noopener"} by David Klee

[Back to top](#top)

---

<a name="170"/>

## Not Using Modern Database Driver
**Check Id:** 170 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+Modern+Database+Driver)

It is generally recommended to keep database drivers up-to-date. Updating drivers ensures compatibility with the latest database versions.

Microsoft recommends using the database driver named [Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL or MSOLEDBSQL19 for encryption changes) 🗗](https://learn.microsoft.com/en-us/sql/connect/oledb/oledb-driver-for-sql-server?view=sql-server-ver16#3-microsoft-ole-db-driver-for-sql-server-msoledbsql-recommended){:target="_blank" rel="noopener"}. 

Previous database drivers versions like [Microsoft OLE DB Provider for SQL Server (SQLOLEDB) 🗗](https://learn.microsoft.com/en-us/sql/connect/oledb/oledb-driver-for-sql-server?view=sql-server-ver16#1-microsoft-ole-db-provider-for-sql-server-sqloledb){:target="_blank" rel="noopener"}, and [SQL Server Native Client (SNAC) 🗗](https://learn.microsoft.com/en-us/sql/connect/oledb/oledb-driver-for-sql-server?view=sql-server-ver16#2-sql-server-native-client-snac){:target="_blank" rel="noopener"} have either been deprecated or no longer maintained. See [Driver history for Microsoft SQL Server 🗗](https://learn.microsoft.com/en-us/sql/connect/connect-history?view=sql-server-ver16){:target="_blank" rel="noopener"} other database drivers not recommended for new application development.

- See [Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL) 🗗](https://learn.microsoft.com/en-us/sql/connect/oledb/oledb-driver-for-sql-server?view=sql-server-ver16#3-microsoft-ole-db-driver-for-sql-server-msoledbsql-recommended){:target="_blank" rel="noopener"} by Microsoft
- See [Driver history for Microsoft SQL Server 🗗](https://learn.microsoft.com/en-us/sql/connect/connect-history?view=sql-server-ver16){:target="_blank" rel="noopener"} by Microsoft

[Back to top](#top)

---

<a name="172"/>

## Schema Drift Not Handled
**Check Id:** 172 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Schema+Drift+Not+Handled)

Schema drift is a gradual change in the structure in a database. It occurs when a target database deviates from the baseline used to originally deploy it. Schema drift can impact all the database objects like tables, views, stored procedures, functions, ...

- See [Not Using Source Control](/best-practices-and-findings/sql-code-conventions#73)

[Back to top](#top)

---

<a name="175"/>

## Not Using SET ARITHABORT to ON
**Check Id:** 175 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+Using+SET+ARITHABORT+to+ON)

[Always set ARITHABORT to ON in your logon sessions. Setting ARITHABORT to OFF can negatively impact query optimization, leading to performance issues. 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-arithabort-transact-sql?view=sql-server-ver16#remarks:~:text=Always%20set%20ARITHABORT%20to%20ON%20in%20your%20logon%20sessions.%20Setting%20ARITHABORT%20to%20OFF%20can%20negatively%20impact%20query%20optimization%2C%20leading%20to%20performance%20issues.){:target="_blank" rel="noopener"} by Microsoft

[If SET ARITHABORT is OFF, CREATE, UPDATE, INSERT, and DELETE statements on tables with indexes on computed columns or indexed views fail. 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-arithabort-transact-sql?view=sql-server-ver16#remarks:~:text=If%20SET%20ARITHABORT%20is%20OFF%2C%20CREATE%2C%20UPDATE%2C%20INSERT%2C%20and%20DELETE%20statements%20on%20tables%20with%20indexes%20on%20computed%20columns%20or%20indexed%20views%20fail.){:target="_blank" rel="noopener"} by Microsoft

If [ANSI_WARNINGS has a value of ON and the database compatibility level is set to 90 or higher then ARITHABORT is implicitly ON 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-arithabort-transact-sql?view=sql-server-ver16#remarks:~:text=When%20ANSI_WARNINGS%20has%20a%20value%20of%20ON%20and%20the%20database%20compatibility%20level%20is%20set%20to%2090%20or%20higher%20then%20ARITHABORT%20is%20implicitly%20ON%20regardless%20of%20its%20value%20setting){:target="_blank" rel="noopener"}, you are good.

- See [SET ARITHABORT 🗗](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-arithabort-transact-sql){:target="_blank" rel="noopener"} by Microsoft
- See [Slow in the Application, Fast in SSMS? - 2.5 The Default Settings 🗗](https://www.sommarskog.se/query-plan-mysteries.html#defaultsettings){:target="_blank" rel="noopener"} by Erland Sommarskog
- See [Troubleshooting Parameter Sniffing Issues the Right Way: Part 2 🗗](https://www.brentozar.com/archive/2018/03/troubleshooting-parameter-sniffing-issues-right-way-part-2/){:target="_blank" rel="noopener"} by Brent Ozar

[Back to top](#top)

---

<a name="181"/>

## Unused Database Objects
**Check Id:** 181 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Unused+Database+Objects)

Remove unused database objects. Rely on the version control system to keep track of previous objects.

Removing unused objects helps in keeping the database schema clean and organized. It improves readability and understanding of the database structure for developers, administrators, and other stakeholders.

Unused datbase objects can pose security risks if they contain sensitive data or are not properly secured. By removing these objects, we reduce the potential attack surface and enhance the overall security posture of the database.

If you need to create a temporary database object like a table using a naming pattern like dbo.MyTempTable_DELETE_ME_AFTER_YYYY_MM_DD. This allows your future self or others to know when it is safe to drop the object.

```sql
CREATE TABLE dbo.MyTempTable_DELETE_ME_AFTER_YYYY_MM_DD ( /* ← Look here */
    SomeColumn    nvarchar(100)
   ,AnotherColumn nvarchar(100)
);
```

- See [Cleaning up Old Code and Unused objects in SQL Server 🗗](https://www.sqlshack.com/cleaning-up-old-code-and-unused-objects-in-sql-server/){:target="_blank" rel="noopener"} by SQLShak
- See [Commented Out Code](/best-practices-and-findings/sql-code-conventions#132)

[Back to top](#top)

---

<a name="183"/>

## Using SQL Server to Send Emails
**Check Id:** 183 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Using+SQL+Server+to+Send+Emails)

Do not use SQL Server to send email. SQL Server's email delivery is not very robust. Sending agent alerts when a database job fails is fine. When email delivery stops, you won’t notice. SQL Server’s email troubleshooting is almost nonexistent.

- See [Don’t Use SQL Server to Send Emails. 🗗](https://www.brentozar.com/archive/2021/12/dont-use-sql-server-to-send-emails/){:target="_blank" rel="noopener"} by Brent Ozar
- See [Send Recurring Emails using C# .NET and Hangfire with SendGrid 🗗](https://www.twilio.com/en-us/blog/recurring-emails-csharp-dotnet-with-hangfire-sendgrid){:target="_blank" rel="noopener"} by Twilio

[Back to top](#top)

---

<a name="185"/>

## Not using group Managed Service Account (gMSA)
**Check Id:** 185 [None yet, click here to add the issue](https://github.com/kevinmartintech/sp_Develop/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=Not+using+group+Managed+Service+Account+(gMSA))

A group Managed Service Account, also known as gMSA, provides enhanced security compared to standard managed service accounts. They are designed for automated processes, applications, services, or tasks that need credentials but don't involve user interaction.

When possible use gMSA for SQL Server as a best practice. gMSAs like "DOMAIN\gMSA$" provide automatic password management and rotation, enhancing security by reducing the risk of password-related vulnerabilities. gMSAs can also be used across multiple servers, making them ideal for clustered or distributed SQL Server environments, while simplifying administration tasks such as password updates.

To use gMSA for an application, run the app as a Windows service, or launched with PsExec.exe, execute via scheduled tasks, or code in an IIS App Pool.

- See [Group Managed Service Accounts Overview 🗗](https://learn.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview){:target="_blank" rel="noopener"} by Microsoft
- See [Service accounts 🗗](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-service-accounts){:target="_blank" rel="noopener"} -> [Group-managed service accounts 🗗](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-service-accounts#group-managed-service-accounts){:target="_blank" rel="noopener"} by Microsoft
- See [Configure your app to use a gMSA 🗗](https://learn.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/gmsa-configure-app){:target="_blank" rel="noopener"}{:target="_blank" rel="noopener"} by Microsoft
- See [PsExec.exe 🗗](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec){:target="_blank" rel="noopener"}{:target="_blank" rel="noopener"} by Microsoft
- See [How to Logon Interactively with a Group Managed Service Account 🗗](https://www.patrickkeisler.com/2022/03/how-to-logon-interactively-with-a-group-managed-service-account/){:target="_blank" rel="noopener"} by Patrick Keisler

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