---
title: Data Conventions
permalink: best-practices-and-findings/data-Conventions
parent: Best Practices & Findings
nav_order: 5
layout: default
---

# Data Issues
{: .no_toc }
Checks for data issues.

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

A placeholder is an empty row/record that is created to hold the place of possible future data in the row that may or may not be necessary.

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