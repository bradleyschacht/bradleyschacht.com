---
title: "Division: SQL Server Style"
date: "2011-03-22"
categories: 
  - "sql-server"
---

When doing calculations in SQL Server queries it is important to know that anything without a decimal is going to be treated as an integer and therefore will cause the result to be an integer as well.  For instance, lets take 598/128 and look at some results.  This will obviously give us a decimal for a result...or at least it SHOULD. SELECT 598/128 = 4 This is clearly NOT an even 4, however SQL has decided to round the result even without us telling it to. Now lets take a look at SELECT CONVERT(DECIMAL(7,5),598/128).  Strangely enough, that also evaluates to 4. If you want to force SQL to return the correct result, in this case a decimal, you need to simply add a decimal to any number in the expression.  This will force the result to be evaluated and displayed as a decimal value, not a rounded integer.  The following, just adding a decimal after 598 now brings back the correct result: SELECT 598.0/128 = 4.61745 Try running each of these three select statements on your computer to see the differece between the outputs.  And remember, if you want precision in your result, not rounding, just add a decimal placeholder. SELECT 598/128 SELECT CONVERT(NUMERIC,598)/128 SELECT 598.0/128 SELECT 598./128
