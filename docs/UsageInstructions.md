---
title: Usage Instructions
permalink: usage-instructions
nav_order: 3
layout: default
---

# sp_Develop Usage Instructions

After installing the [sp_Develop 🗗](https://raw.githubusercontent.com/kevinmartintech/sp_develop/master/sp_Develop.sql){:target="_blank" rel="noopener"} stored procedure, open SQL Server Management Studio and run in the database you wish to check for database development best practices.

```sql
EXECUTE dbo.sp_Develop;
```

That's the bare minimum you need to run the best practice checks!

If you are new to sp_Develop, it is recommended you start with the SQL statement below to limit the number of findings.

```sql
EXECUTE dbo.sp_Develop @PriorityOrHigher = 'High';
```

Visit [Parameter Explanations for more options](parameter-explanations)

[What the results mean](results-explanations){: .btn .btn-green}
[View on GitHub 🗗](https://github.com/kevinmartintech/sp_Develop){: .btn .btn-blue}{:target="_blank" rel="noopener"}