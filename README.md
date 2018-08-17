# MS-SQL Cheatsheet
## Queries
### List mssql running queries
Show a list of all running queries, including their runtime and query-text.
This is the MS-SQL equivalent to mySQL "show full processlist".
```
SELECT sqltext.TEXT,
req.session_id,
req.status,
req.command,
req.cpu_time,
req.total_elapsed_time / 1000 as total_elapsed_time_SECONDS
FROM sys.dm_exec_requests req
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS sqltext
order by req.total_elapsed_time desc
```

## Views
### Show mssql view definition
Show the full definition of a view. The alternative (using INFORMATION_SCHEMA) has a limit of 4000 characters, and should be avoided.
```
EXEC sp_helptext 'dbo.nameOfTheView'
```
### Search for mssql view by definition
```
select o.*, m.definition
from sys.objects     o
join sys.sql_modules m on m.object_id = o.object_id
where o.type = 'V'
  and m.definition like '%textToSearchFor%'
```
## Stored Procedures
### List mssql stored procedures
```
SELECT *
FROM INFORMATION_SCHEMA.ROUTINES
WHERE ROUTINE_TYPE = 'PROCEDURE'
```
### Search for mssql stored procedures by definition
```
SELECT *
FROM INFORMATION_SCHEMA.ROUTINES
WHERE ROUTINE_TYPE = 'PROCEDURE'
AND ROUTINE_DEFINITION LIKE '%textToSearchFor%'
```
## Triggers
### List all mssql triggers, including their definition.
```
SELECT
	Tables.Name TableName,
	Triggers.name TriggerName,
	Triggers.crdate TriggerCreatedDate,
	Comments.Text TriggerText
FROM sysobjects Triggers
INNER JOIN sysobjects Tables On Triggers.parent_obj = Tables.id
INNER JOIN syscomments Comments On Triggers.id = Comments.id
WHERE
	Triggers.xtype = 'TR'
	AND
	Tables.xtype = 'U'
ORDER BY
	Tables.Name,
	Triggers.name
```
