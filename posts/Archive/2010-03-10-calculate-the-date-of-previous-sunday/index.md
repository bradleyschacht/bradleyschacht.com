---
title: "Calculate the Date of Previous Sunday"
date: "2010-03-10"
categories: 
  - "sql-server"
---

There are a number of ways that you can calculate dates using T-SQL.  A common practice is to find the first day of the week which can be accomplished easily by using the following function. SELECT DATEADD(wk, DATEDIFF(wk,0,GETDATE()), 0) However, you will notice that this returns the date for Monday as SQL Server uses Monday for the start of the week. If your week starts on Sunday or you need to get the date for the previous Sunday you can simply modify the DATEADD part of the query from adding zero to adding negative 1.  So our query to get the date for the Sunday now becomes the following. SELECT DATEADD(wk, DATEDIFF(wk,0,GETDATE()), -1) One last modification if you do not want to have the timestamp attached is to add a conversion on the beginning.  So now we get the date and no time. SELECT CONVERT(DATE,DATEADD(wk, DATEDIFF(wk,0,GETDATE()), 0)) This is all predicated on the DATEFIRST option in SQL Server being set to Monday.  If it is already Sunday then the -1 is not necessary.
