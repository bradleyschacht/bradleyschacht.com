---
title: "SSIS 2012 Deadlock When Running SSIS from SQL Agent Job"
date: "2013-03-12"
categories: 
  - "business-intelligence"
  - "sql-server"
---

I recently had an interesting situation where an SSIS package was scheduled to run at 1 AM but failed with a deadlock message. This seems a bit strange given that it was a deadlock on the step executing the SSIS package, not an error message from within the package. There was no history of a failure in the SSIS execution logs, just the job failure. As you can see, the error only happened a couple of times even though all the 1:00 AM runs were at the same time as the other job. ![](https://images.bradleyschacht.com/wp-content/uploads/2013/03/SSISDeadLock.png) The error message text read as follows: **Executed as user: <Agent Account Here>. Microsoft (R) SQL Server Execute Package Utility  Version 11.0.2100.60 for 32-bit  Copyright (C) Microsoft Corporation. All rights reserved.    Started:  1:00:01 AM  Failed to execute IS server package because of error 0x80131904. Server: <Server Name Here>, Package path: SSISDB<Folder><Project><SSIS Package Name>.dtsx, Environment reference Id: NULL.  Description: Transaction (<SPID>) was deadlocked on lock resources with another process and has been chosen as the deadlock victim. Rerun the transaction.  Source: .Net SqlClient Data Provider  Started:  1:00:01 AM  Finished: 1:00:09 AM  Elapsed:  8.331 seconds.  The package execution failed.  The step failed.** The reasoning behind this failure is the stored procedure in the SSISDB catalog.create\_execution. If multiple calls are made with that stored procedure at the same time then a deadlock could occur. Essentially that is what happens when you schedule two SSIS packages at the same exact time in SQL 2012, the procedure is run for each job that starts and causes the deadlock error seen above. \[notice\]The following information is a direct copy from [Microsoft Support Document 2699720](http://support.microsoft.com/kb/2699720)\[/notice\] Consider the following scenario:

- You install Microsoft SQL Server 2012.
- You schedule a Microsoft SQL Server Integration Services (SSIS) package to start by using the SSIS.catalog.create\_execution stored procedure.
- You call the SSIS package again or schedule a second SSIS package to start at the same time by using the SSIS.catalog.create\_execution stored procedure.

In this scenario, you may experience a deadlock condition. An error that resembles the following is logged in the SQL Server Agent Job History:

> Description: Transaction (Process ID spid) was deadlocked on lock resources with another process and has been chosen as the deadlock victim. Rerun the transaction.

While the article goes on to say the issue is resolved in Service Pack 1 and is even listed in the bug fixes (link [here](http://support.microsoft.com/kb/2674319) to full list of bug fixes in SP 1) the error occurred for me on a server with the latest service pack installed. The resolution for me was to simply (as suggested in the workaround section of KB 2699720) stagger the timing. One package runs at 1:00 AM the second runs at 1:05 AM. You probably don't need that much time between jobs, but the window I was in didn't need to be very close, so I had some leeway to schedule other processes to start in between.
