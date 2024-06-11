---
title: "Script Indexes With Your Tables"
date: "2010-09-03"
categories: 
  - "sql-server"
---

Scripting tables inside of SQL Server Management Studio is a very simple task.  Just right click and select script table.  The problem is that, but default, the indexes on a table are not part of the script.  Rather than scripting the indexes separately from the table you can tell management studio that you want the indexes included.  Click on Tools then Options and expand the SQL Server Object Explorer section on the left side.  From there select Scripting and scroll down to the section for "Table and view options".  There are numerous options under this section for scripting tables.  The one we are interested in this case is the "Script indexes" setting; this is False by default.  Just change it to true and now whenever you right click and script a table it will include all index, clustered and non-clustered, in the create table script. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/ScriptIndexes.png) Some other usefull settings (some of which are enabled by default that you may want to turn off at some point) are: \*Include IDENTITY property \*Schema qualify foreign key references \*Script CHECK constraints \*Script defaults \*Script foreign keys Script full-text indexes \*Script primary keys Script statistics Script triggers NOTE: A \* before the item denotes that it is enabled by default
