---
title: "Search Stored Procedure Text"
date: "2011-04-26"
categories: 
  - "sql-server"
---

There will come a day, if it has not already come and gone, when you will want to search through your stored procedures because you don't remember which one did what you want to do.  Did that make sense?  Because I don't know if it did.  Anyway, here is what we have...

1. You
2. A bunch of stored procedures
3. Something you are looking for inside one or more of your stored procedures
4. A long night ahead opening each one and using CTRL + F

Luckily there are a couple of super easy solutions.  Below are two such solutions.  The first one is real simple, however, it may not produce the correct results on larger procedures. `SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_DEFINITION LIKE '%Text%'` The problem with this method is that it only stores the first 8000 characters of the stored procedure so you may miss what you are looking for.  Another way to go about searching is by using the following query. `SELECT name AS ProcedureName, OBJECT_DEFINITION(OBJECT_ID) AS ProcedureText FROM sys.procedures WHERE OBJECT_DEFINITION(OBJECT_ID) LIKE '%Text%'` This should get you everything you need without worrying about the procedures that are over 8000 characters long.  There is also another way to query the stored procedures as well as functions.  So I created the little query below that will allow you to search those objects in addition to stored procedures. If you really want to toss a little cursor or while loop around this and iterate through all stored procedures and functions across all databases. `SELECT DB_NAME() AS DatabaseName, CASE WHEN OBJECTPROPERTY(object_id, 'IsProcedure') = 1 THEN 'Stored Procedure' WHEN OBJECTPROPERTY(object_id, 'IsTableFunction') = 1 THEN 'Table Function' WHEN OBJECTPROPERTY(object_id, 'IsScalarFunction') = 1 THEN 'Scalar Function' WHEN OBJECTPROPERTY(object_id, 'IsInlineFunction') = 1 THEN 'Inline Function' ELSE NULL END AS ObjectType, OBJECT_SCHEMA_NAME(object_id) AS ObjectSchema, OBJECT_NAME(object_id) ObjectName, definition AS ObjectText FROM sys.sql_modules WHERE OBJECTPROPERTY(object_id, 'IsProcedure') = 1 OR OBJECTPROPERTY(object_id, 'IsTableFunction') = 1 OR OBJECTPROPERTY(object_id, 'IsScalarFunction') = 1 OR OBJECTPROPERTY(object_id, 'IsInlineFunction') = 1 AND definition LIKE '%Text%'` Hope you enjoy!
