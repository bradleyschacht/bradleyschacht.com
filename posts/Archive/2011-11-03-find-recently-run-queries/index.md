---
title: "Find Recently Run Queries"
date: "2011-11-03"
categories: 
  - "sql-server"
---

If you have ever closed a query window by accident only to wish you could get back your work an instant later you may be in luck.  If you ran the query in question there is hope for you.  The query below will return queries that have been run for you including the time and query text.  I haven't figured up a way to get the user that executed the query just yet.  So if you have that tidbit to add to this so you can filter a little easier please let us know. You can narrow the list by adding a where clause and searching for some text you know was in the query or looking around a specific time period.  The text wont be formatted pretty when you copy it, but reformatting is better than rewriting! Without further ado, here is the magic: `SELECT a.last_execution_time AS ExectuionTime, b.text AS Query FROM sys.dm_exec_query_stats a CROSS APPLY sys.dm_exec_sql_text(a.sql_handle) b ORDER BY a.last_execution_time DESC`
