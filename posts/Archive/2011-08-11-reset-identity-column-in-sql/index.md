---
title: "Reset Identity Column in SQL"
date: "2011-08-11"
categories: 
  - "sql-server"
---

I'll be honest, I am partially writing this because I always forget how to reset the identity column on a table and I want to be able to just pull up my blog and look it up.  Yet again today I had to do this and I couldn't remember the exact syntax, so here it is.  The next record is going to be the seed value plus 1, or in the case of this example I will use zero so the first record is 1.  That will make my O.C.D. happy to see my table starting with a key value of 1! Hopefully this will help some people new to T-SQL and be a simple reminder for the veterans out there. Syntax: `DBCC CHECKIDENT('TableNameHere', RESEED, StartingValue - 1)` Example: `DBCC CHECKIDENT('DimGeography', RESEED, 0)`
