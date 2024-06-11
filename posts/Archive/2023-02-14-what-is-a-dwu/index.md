---
title: "What is a DWU?"
date: "2023-02-14"
categories: 
  - "azure"
  - "syndication"
---

You're new to Azure Synapse Analytics but you know you want to build a data warehouse. What do you do? After a quick search of the internet, you discover you need a Synapse dedicated SQL pool. Log into Azure, click a few buttons, deploy a Synapse workspace, and it's time to get started with the dedicated SQL pool but there is one problem...you have no idea what a DWU is.

![](https://images.bradleyschacht.com/wp-content/uploads/2023/02/image.png)

The default is 1,000 of them and they aren't cheap.

Do you need 1,000? 100? 8,278?

These are all good questions, but let's try to address the "What is a DWU?" question first.

## What is a DWU?

A DWU, or Data Warehouse Unit, is the Synapse dedicated SQL pool's blended representation of compute power. Similar to the early days of Azure SQL Database where we only had DTUs, you won't find vCores or memory listed anywhere on the page or documentation.

This number drives a number of things that we will cover in separate posts at a later date, but at the core it combines CPU, memory, and IO. The used DWU percentage will therefore be represented by the maximum of those three metrics. For example, a workload that is very CPU intensive using 60% of the available CPU for the currently selected DWU, but uses very little memory at 10% and a small amount of IO at 15% would show as using 60% of the available DWU. Similarly, an IO intensive workload using 5% CPU, 15% memory, and 40% of the provisioned IO would show as using 40% of the available DWU.

## How do I choose the right number of DWUs?

To oversimplify things, if you need less overall performance then go with a lower DWU. If you need more overall performance, then go with a higher DWU.

I'm a former consultant, so I am well trained in saying "it depends". To that end, in reality, the number of required DWUs is very workload specific. Here are a few guiding principals for helping land on the right setting:

- Do you initial development on a very small number of DWUs. Start with 100 DWUs just get the DDL deployed even.

- DW100 to DW500 are all a single compute node with varying levels of compute power. That means you are really running a single node server with the overhead of an MPP engine. Not until DW1000 do you get a second compute node.

- When doing load tests, use at least DW1000. Anything lower shouldn't be considered for production and should only be used for development purposes.

- Use a tool like JMeter to simulate a workload. Be sure to look at the whole workload.
    - Build a simulation for your ETL.
    
    - Build another for ad hoc user queries.
    
    - Build another for reporting tool queries.
    
    - Run them in series or parallel depending on expected loading and query patterns.
    
    - Use realistic data sizes. Don't build the simulation on 100k records when you're going to have 100 billion records. It doesn't need to be a 100% match on data size, but it should be representative.

- Scale up or down based on the performance observed in your test.

- Don't forget to account for bursts of activity and plan to scale appropriately. A monthly or quarterly load may mean you scale up a few hours in advance or at some logical point where you can take a few minutes of downtime for the scaling operation to complete.

- [DWUs drive concurrency](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/memory-concurrency-limits#concurrency-maximums-for-workload-groups). Maximum concurrency, 128 queries, in not unlocked until DW6000c.

- The more CCI rebuilds you want to run in parallel, the more memory you will need, the more DWUs you will need.

- The moral of the story is this...test, test, test.

## How do I know what DWU I'm running?

The current SQL pool service level will be shown in the Azure Portal, Synapse Studio, PowerShell, and through the T-SQL query below when connected to the master database.

SQL

```
SELECT
	d.name AS DatabaseName,
	Edition AS DatabaseEdition,
	service_objective AS ServiceObjective
FROM sys.database_service_objectives AS dso
INNER JOIN sys.databases AS d
	ON dso.database_id = d.database_id
```

There you have it. Data warehouse units simplified!
