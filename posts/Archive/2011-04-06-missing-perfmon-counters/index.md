---
title: "Missing Perfmon Counters"
date: "2011-04-06"
categories: 
  - "windows"
---

Perfmon is a great tool to get a closer look at what is going on in your environment as queries and packages run.  There are a ton of counters ranging from OS to third party.  In our case we are concerned with SQL Server.  I know, that was pretty obvious.  The problem is from time to time things break, and therefore counters can disappear.  It could be something as simple as a registry key doesn't get updated correctly when a hotfix is installed or the common occurance when an in place upgrade from 2005 to 2008 takes place.  Who knows, maybe we will even get the luxury of that being a feature of the 2008 to Denali upgrade process!! ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/Perfmon1.png) But I digress. I want my counters!  So, how do you get them back?  It's actually quite simple, run a simple command on the command prompt called lodctr (load counter).  There are two sets of counters that I am going to mention in the set of steps listed below, SSIS and SQL Server.  They are in two different locations, so be sure to pay attention to which one you want to restore.  There is one .ini file to restore for SSIS and two for SQL Server. It is worth noting that sometimes only some of the counters will not appear, as in the case that I had where only one of the SSIS counters didn't show up.  You can still run the above command and all the counters that are missing will be added in.  Instructions are below.  Good luck! 1. Locate the file perf-DTSPipeline100DTSPERF.INI on the file system.

- It should be located in the folder Microsoft SQL Server100DTSBinn.
- If it is not present, copy it from another machine.

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/Perfmon2.png) 2. Open the command prompt (Start > Run > cmd) 3. Run the following command at the command prompt.

- lodctr D:"program files""microsoft sql server"100dtsbinnperf-DTSPipeline100DTSPERF.INI
- Note that you will need to put any portion of the path with spaces inside of double quotes (for instance Program Files should be …“Program Files”...)

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/Perfmon3.png)

4\. If the SQL Server counters are not present (in addition to the SSIS Counters) you can follow the same process with the following files that should be located in Microsoft SQL ServerMSSQL10\_50.MSSQLSERVERMSSQLBinn.  There are two counters in this case:

- perf-MSSQLSERVERsqlctr.ini
- perf-SQLSERVERAGENTsqlagtctr.ini

 ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/Perfmon4.png)
