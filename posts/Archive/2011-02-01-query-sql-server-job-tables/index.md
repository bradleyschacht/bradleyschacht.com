---
title: "Query SQL Server Job Tables"
date: "2011-02-01"
categories: 
  - "sql-server"
---

If you have ever wanted to query the SQL Server job tables then you know they are a little cryptic. The following query will assist you in getting some useful information out of those tables. I recently wanted to be able to query these tables and get the error for when there was a failure during the running of the job. This can be accomplished using this query and just adding another couple of fields. You can add the job history (jh alias in this query) sql\_severity and message columns. Then you can simply do a where statement to search the message column for a particular error or for particular keywords that pertain to errors, such as something novel like "error"!! Hope this helps. You can also get some useful information from the table msdb..jobactivity. SELECT CONVERT(DATETIME, RTRIM(jh.run\_date)) + (jh.run\_time \* 9 + jh.run\_time % 10000 \* 6 + jh.run\_time % 100 \* 10) / 216e4 AS RunDateTime, j.name AS JobName, jh.step\_id AS StepID, SUBSTRING(RIGHT('000000' + CONVERT(varchar(6), jh.run\_duration), 6), 1, 2) + ':' + SUBSTRING(RIGHT('000000' + CONVERT(varchar(6), jh.run\_duration), 6), 3, 2) + ':' + SUBSTRING(RIGHT('000000' + CONVERT(varchar(6), jh.run\_duration), 6), 5, 2) AS StepRunTime, jh.step\_name AS StepName FROM msdb..sysjobhistory jh INNER JOIN msdb..sysjobs j ON jh.job\_id = j.job\_id ORDER BY jh.run\_date, jh.run\_time, j.job\_id, jh.step\_id
