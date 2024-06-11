---
title: "Find SQL Database Owner with T-SQL"
date: "2014-04-29"
categories: 
  - "sql-server"
---

One thing that on occasion bugs me about PDW or APS or whatever it happens to be called this week is the lack of a UI to do things. I'm not one of those people who has to have a UI to perform operations. I have no problem creating databases, doing backups or creating tables through code. That being said there are times when a UI is helpful to just look up information. Just today I needed to find out who the database owner was and had to do so through T-SQL. If you look at the sys.databases table you will notice there is an owner\_sid column but to the naked eye it is pretty useless. So here is what you need, wrap ownser\_sid in syser\_sname and you are good to go. Find the code below. Enjoy! `SELECT suser_sname(owner_sid), * FROM sys.databases`
