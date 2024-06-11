---
title: "New Azure SQL Database Pricing Model"
date: "2018-04-12"
categories: 
  - "azure"
  - "sql-server"
  - "syndication"
tags: 
  - "azure"
  - "database"
  - "sql"
---

* * *

Azure SQL Database has some great built-in features that are included in the price like automatic backups, high availability, auditing and threat detection, transparent data encryption. No matter how excited people are when I talk to them about Azure SQL DB there is one topic that causes confusion. Every. Single. Time. That topic is sizing. "What the heck is that DTU thing and how many of theme do I need?" It's a valid question. The DTU setting on a database drives the performance and price. Making the setting too low and your application will underperform expectations. Set it too high and you're wasting money paying for more resources than necessary. I've heard people say "It's a cloud environment and you can scale up and down so it shouldn't really matter what starting point you choose." but I don't think that's right. You should know what you are paying for. You should be able to figure out how many DTUs you need to make your application run but not over provision to cause waste. Thankfully there are a websites like the DTU Calculator that help out. Wouldn't it be great if you didn't need to run your stats through tool to estimate your needs? Wouldn't it be great if you could size and purchase in terms that today's DBAs understand? I'm happy to say that is becoming a reality today! Now in preview is vCore based purchasing options in addition to DTUs. If you like the DTU model it will still be there. But if you want flexibility and want to know exactly what is behind your database then go choose the vCore purchasing option. In the [Azure Portal](http://portal.azure.com) you'll navigate to the database you want to size with vCores (or create a new database). Next, go to the **Resource Configuration & Pricing** settings (this used to be labeled Pricing Tier or Scale or something along those lines). Across the top of the screen you'll notice the same options from before, Basic, Standard, Premium, Premium RS, and now a new option labeled **vCore-base pricing options**. Select the vCore option. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/new-azure-sql-database-pricing-model-001.png) Because, at the time of this writing, the vCore pricing is in preview you must accept the preview terms before being able to save your selections. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/new-azure-sql-database-pricing-model-002.png) At the current configurations the following are resource allocations:

- 7GB of memory per vCore
- Throughput is based on the service tier
    - General Purpose is 500 IOPS per vCore
    - Business Critical is 5000 IOPS per vCore

A great benefit of the vCore pricing model is the ability to leverage your on-prem SQL licenses to bring down the cost. This is accomplished through the [Azure Hybrid Use Benefit for SQL Server](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing). This can reduce the cost of a database by up to 30%. That's an advantage you won't find anywhere except Azure SQL! In a later post I will dive a little deeper into the differences between General Purpose and Business Critical as well as the technical details behind the differences between DTUs and vCores to help choose the right levels should you decide to move from DTU to vCore pricing.
