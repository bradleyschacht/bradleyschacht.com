---
title: "SSIS 2012 Change Data Capture (CDC) Control Task"
date: "2012-07-10"
categories: 
  - "business-intelligence"
  - "sql-server"
---

Change data capture was introduced in SQL Server 2008 and has been pretty popular ever since. The basic concept behind it is that all changes (inserts, updates, deletes) are captured and the details made available in a consumable manner. This means ultimately that all changes will be written to a table you can query to see everything that has been going on. In SSIS 2008 it was a little cumbersome to maintain and get data out of the CDC tables. SSIS 2012 has made that much easier with the addition of three components specifically for change data capture. In this blog we will explore the CDC Control Task. **CDC Control Task Information** The initial load of the data is the most challenging part of the change data capture process from a component standpoint. According to books online the CDC components support three initial loading and change processing scenarios:

1. Initial loading done with a database snapshot. In this case, change processing starts with the LSN of the snapshot event.
2. Initial loading from a quiescent database. In this case, no changes are made during initial loading so the current LSN is sampled at sometime during the initial load and change processing starts with that LSN.
3. Initial loading from an active database. In this case, as the initial load is in progress, changes are made to the database and there is no single LSN from which change processing can be precisely started. In this case, the initial load package developer can sample the source database current LSN before and after the initial load. Then, when processing changes, care should be taken when processing changes made in parallel to the initial load as some of the processed changes are already seen in the initial load (for example, an Insert change may fail with a duplicate key error because the inserted row was read by the initial load process).

The CDC Control Task supports two different types of operations:

- Sync of initial load and change processing
- Management of processing range

The following operations can be performed by the CDC Control Task (again, available in books online as well):

- Reset CDC State: I may be missing something, but this is listed online but not available in the actual product. So I will skip it for now.
- Mark Initial Load start: Records the LSN in the source database before the initial load reads any data from the source table. Used in conjunction with scenario 3 above.
- Mark Initial Load end: Records the LSN in the source data after the initial load is done loading data from the source table. Used in conjunction with scenario 3 above.
- Mark CDC Start: Records the current LSN and indicates that change processing should start from that point forward. Used in conjunction with scenario 1 and scenario 2 above.
- Get Processing Range: Used before a data flow so the CDC source knows what LSNs should be read from.
- Mark Processed Range: Used after a data flow to indicate the last LSN that was processed. This becomes the LSN that will be the start of the range next time a Get Processing Range operation is performed.

**Configuring the CDC Control Task** The CDC Control Task can be found in the SSIS Toolbox inside Data Tools. This is an SSIS 2012 only feature. It will be listed under **Other Tasks** by default. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/CDC-Control-Task-1.png) Here is a completed CDC Control Task with a brief description of what each option is for: ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/CDC-Control-Task-2.png)

1. **SQL Server CDC database ADO.Net connection manager:** Connection to a SQL database where CDC is enabled on the database level and on at least one table.
2. **CDC control operation:** Operation for the task. The option chosen here will use the variable in the next option to store any required information.
3. **Variable containing the CDC state:**  Stores information from the previous option and passes it between the task, control flow source and potentially other CDC control tasks.
4. **SQL Server LSN to start the CDC or Snapshot database name:** Current source database LSN or snapshot database name where the initial load performed from. This indicates where CDC should start. Only used when option 2 is set to Mark CDC Start. Leaving this blank will default to the current LSN.
5. **Connection manager for the database where the state is stored:** Connection to the database where the state table is stored.
6. **Table to use for storing state:** Table where the CDC state is stored. I know that was an amazing explanation. :)  What can I say, the name explains itself.
7. **State Name:** Used to look up the state in the table from option 6 above.

There is an additional option for Automatically store state in a database table. I recommend just keeping that checked. If you do not check that option you have to load the CDC state when the process starts and save the state when the process ends manually.

Now for a quick step by step on actually configuring the task... Requirements for using this task include a database where CDC is enabled, an ADO.NET connection to the database where changes are tracked, a table to store the current CDC state and a variable for storing the current CDC state in the package. All of these can be created from the task itself with the exception of enabling CDC on the database and table(s).

1. Select a new ADO.Net connection manager or create a new one from the task.
2. Set the CDC control operation based on the uses listed above
    1. Initial load start, initial load end or CDC start for the initial load
    2. Get processing range or mark processed range for subsequent loads
3. Select or create a new a variable to store the CDC state
4. You can likely leave the LSN to start CDC empty which will just use the current LSN and keep the store state in database box checked
5. Choose the connection you created to the CDC database again in the connection manager for the database where the state is stored option, or if there is a separate database with this information create a connection to that database and select it.
6. If this is the first time you are setting up the state click New next to the table to use for storing state option. If your table is already set up select it from the drop down menu.
7. Finally choose the state name, if you are using all the defaults it will be CDC\_State. There will be one record in the state table for each unique state. So if you want to maintain each table separately then you should create a different state for each table or group of tables.

There is a lot of information in here, so please ask questions if you have any. I think the most important piece to this whole task is when it is used to configure the initial load. I did an initial load using the mark initial load start and end options when first testing the task out. To my surprise when I ran the data flow to pick up changes it came back with nothing. The second time I ran it however all my changes were picked up. This was because I was loading data from a static database and should have just chosen to load the data then do an operation of Mark CDC start. I will be posting a blog about each of the data flow components then I will post an SSIS package that sets up a database for CDC, does the initial load and then does a changes load. Be on the lookout for those in the next couple of weeks. I have the SSIS package already built, but I want to be sure to go through each of the three components first so that everyone understands what each option does. Then you can avoid my mistake of choosing the wrong options and spending an hour or two trying to figure it out before finally looking at books online. One final note, at the time of writing this post there are several errors/clarifications needed in the books online documentation that are known issues and are supposed to be in the process of being fixed.
