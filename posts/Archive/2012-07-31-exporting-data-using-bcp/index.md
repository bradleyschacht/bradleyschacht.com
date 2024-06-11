---
title: "Exporting Data Using BCP"
date: "2012-07-31"
categories: 
  - "sql-server"
---

BCP, or bulk copy program, has been around in SQL Server for a long time. It is a great way to export large quantities of data very quickly from SQL Server. It can be used to export entire tables or even a custom query. In this post we will focus on doing some simple commands to export data. This is by no means a complete and comprehensive look at BCP. My only intention with this post is to get you started and maybe provide a quick syntax reference for doing BCP in the future. The obvious choice to move data out to a flat file may be SSIS. BCP could not be any more of a polar opposite in actually implementing the data export in that SSIS is a completely developed in a GUI and BCP is completely developed at the command prompt. To get started open up a new command prompt window on your computer. You may want to go ahead and run it as administrator in case you want to put the file someplace like the C: drive and have UAC turned on. If you simply type BCP at the command prompt a series of available commands will be displayed. We will only touch on a couple of those: ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/BCP1.png) You can see in the screenshot the basic syntax for BCP is \[What to Export\] \[in or out\] \[file\] BCP AdventureWorks.Production.Product out C:Production\_Product.txt The second parameter can actually have one of four values:

- in - Copy data into a table or view from a file
- out - Copy data from a table or view to a file
- queryout - Copy data from a query to a file (query must be provided enclosed in quotes, not a table or view name)
- format - Creates a format file based on the table, view or query specified

There are several other parameters you will want to be sure to include keeping in mind each is case sensitive:

- **\-S** ServerNameInstanceName
- Authentication
    - **\-U** Username and **\-P** Password
    - **\-T** Windows Authentication
- Data Types
    - **\-c** Character data types
    - **\-n** Uses the native data types from the source system
    - Do not specify anything and you will be prompted to provide a data type for each column

Now for a couple of samples: For each of these I am going to be using windows authentication to connect to a named instance on my local machine. This can connect to other servers however and the files can also be sent to a network share as well. Table Export: BCP AdventureWorks.Production.Product OUT C:ProductionProduct.txt -S localhostSQL2008R2 -T -c Notice that 504 rows were exported in well under a second at 504,000 rows per second. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/BCP2.png) Query Export: **BCP "SELECT \* FROM AdventureWorks.Production.ProductModel" QUERYOUT C:ProductionProductModel.txt -S localhostSQL2008R2 -T -c** The results in this case are similar: 128 rows exported, still under a second and 1,376 rows per second. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/BCP3.png) Notice what happens when providing a query and specifying OUT instead of QUERYOUT: Unfortunately the error is not all that great, we are just told that an error occurred while processing the command line. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/BCP4.png) Just to show what happens when exporting a table with more than just a couple of records here is a screenshot of the export from ContosoRetailDW.dbo.FactOnlineSales ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/BCP5.png) Hope this sheds some light on exporting data with BCP. Be on the lookout for some information coming on importing data with BCP as well as a performance comparison between BCP and SSIS direct to a flat file. Want to explore the BCP options in more detail? Head over to the MSDN page: [http://msdn.microsoft.com/en-us/library/ms162802(v=sql.105).aspx](http://msdn.microsoft.com/en-us/library/ms162802(v=sql.105).aspx)
