---
title: "Public and Demo Data Coming Soon!"
date: "2019-08-22"
categories: 
  - "datasets"
  - "syndication"
---

Any database professional that has been around the Microsoft world for more than about 3 minutes will be familiar with the old, faithful sample dataset created and published by Microsoft: Adventure Works.

The Adventure Works databases follow the sales of a (fictional) multinational conglomerate looking to take over the work one bicycle sale at a time! The samples included a couple of databases, most notably a transactional (AdventureWorks) and a data warehouse (AdventureWorksDW). While those databases can still be downloaded and are widely used in demos, there is a newer version of sample databases going under the name Wide World Importers that was introduced in 2016. If you’ve been around a bit longer or know some older more seasoned DBAs you may hear about databases like Northwind or Pubs. As a consultant I occasionally would find those databases on production servers. One time a DBA even told me they couldn’t remove the Northwind database because it would break one of the cross database joins in their reporting database. That was a fun project. Let’s fast forward to modern day…

The sample databases of old were ok for their time. The problem with all of them is that they are boring, small, and don’t really do a great job of teaching. It’s hard to really dive deep and explore the impact of rebuilding an index on a table that is only 50MB. It’s difficult to see the impact of switching from a CTE to a temp table when your total dataset is 30,000 records. It’s good for learning the basics of interacting with SQL, but it’s not great for deep dive or demos. You can immediately see how uninterested people look when you switch from your slide deck over to SSMS and they only see AdventureWorks and AdventureWorksDW in the object explorer. The other issue is that the data world is expanding. We need to be able to see how things work on large datasets both on-prem and what the impact will be of moving those to the cloud. You can’t learn about parallel loading with PolyBase on AdventureWorks size data. Finally, data use cases are expanding faster than ever. Even just a few years ago a data professional would pretty much be dealing with relational databases and that’s all. Today, more and more people are starting to explore technologies like Databricks for data processing and machine learning. So what is the solution?

I try to find interesting datasets from all over the place. There are a lot of good sources to get data and I will try to centralize some of the ones I use on this page. The list will evolve over time, and I’m always open to suggestions on new datasets. Some of these will be medium-large datasets (within reason, likely not getting into the TB of data but certainly 10’s to 100’s of GB), some will be smaller but interesting datasets (you can pull neat stuff from a lot of government open data portals), and some will just be data I find interesting (airline on-time because I like planes and I spend a lot of time traveling). The data will also come in various shapes. Most of the freely available data is CSV or XML. Occasionally you’ll find a database backup, JSON dump, or a streaming dataset. I try to get a little of everything since my world revolves around things like Azure SQL, Databricks, and Azure Stream Analytics workloads.

**What will you find here?**  
I am trying to make these datasets as easy to use as possible. Sometimes I may just post a link to a dataset, but I will try my best to provide code to make the data usable. For instance, you may find a download with some PowerShell scripts that download zip files, unzip the CSV contents, and BCP them into a database. Other times, you may just get a link and you’ll have to figure it out for yourself. Over time, I’ll try to put a tutorial together for each of the datasets that I use myself.

**What will you not find here?**  
You won’t find completed datasets hosted on my side and available for download. I would love to be able to provide a repository of data, but the reality is that costs money and I’m just a guy who is interested in data. I can’t guarantee any of the scripts will work a week from now as many sources change access policies, datasets, and locations. I will do my best to keep up with the major datasets that I use and will make a note of the last time I made an update to my scripts.

**How should you use these sources?**  
However you’d like. Personally, I take the datasets that I use most often, clean them up, and keep an archive of them in my Azure storage account and/or on an external hard drive.

**When can we expect to grab some sample data?**  
Soon. I'm hoping to have the first one published next week and will be centered around Stack Overflow. Following that will be Airline on-time performance, then NYC Taxi trips (or maybe the other way around). In the meantime I'll be building out the list of data sources that I frequently hit when preparing to show off performance and flashy reporting to my customers or a presentation at various training events. You can find all the updates over at the [Public Datasets](https://bradleyschacht.com/public-datasets/) page.
