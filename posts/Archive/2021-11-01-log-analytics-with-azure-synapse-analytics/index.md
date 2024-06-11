---
title: "Log Analytics with Azure Synapse Analytics"
date: "2021-11-01"
categories: 
  - "azure"
  - "syndication"
---

There are a lot of services in Azure. Way more than a few. What is something you want to do with all your services and applications? You want to monitor them. How do you do that? By looking at the logs that are produced. How do you capture and make sense of thousands of log entries from hundreds of services for this really awesome solution you’ve been working on? Azure Monitor.

This post isn’t about Azure Monitor though. Not directly anyway.

Azure Synapse is no different than any other application or service in your environment. It serves a purpose in the architecture and as a result needs special attention from people with a specific skillset, in this case DBAs and developers. That’s where Azure Monitor comes into the picture and what we will be discussing today: how to make sense of the integration between auditing in Azure Synapse and Azure Monitor. The focus will be on Azure Synapse Analytics Dedicated SQL Pools (Gen 2) specifically, but there is integration for monitoring serverless SQL pools, Spark pools, pipelines, and general workspace operations.

First, those with an eye for detail will notice that this post has Log Analytics in the title and not Azure Monitor. That’s true. A very brief explanation so everyone has things straight in their heads. I put Log Analytics in the title because when you set up auditing or diagnostics the option is labeled “Send to Log Analytics workspace” not “Send to Azure Monitor” and I wanted people to be able to find this post. Azure Monitor is a set of functionalities for collecting and analyzing logs. There are some prebuilt integrations and visualizations with some Azure services like Key Vault or Storage Accounts Then there is Log Analytics, a functionality inside Azure Monitor which also happens to be where Synapse writes its logs, for storing and querying all log data. Queries are written in a Kusto Query Language or KQL.

Second, let’s be sure a few very important details are clear. When referring to Azure Synapse there are two distinct but very similar services. They are both the same Microsoft cloud MPP database engine, both cover the same general T-SQL surface area, both scale in terms of DWUs, both talk about “Azure Synapse Analytics” in the documentation and description, but one is deployed to an Azure SQL Server, and one is deployed to a Workspace.

We need to be very clear about this because they both operate slightly different. Again, this post is focused on Azure Synapse Analytics Dedicated SQL Pools (Gen 2). An alternate post is available for [Dedicate SQL Pools (Formerly SQL DW)](https://bradleyschacht.com/log-analytics-with-dedicated-sql-pools-formerly-sql-dw/) if you need information about that deployment option.

Let’s get started.

# What are my options?

There are two types of logging that can be enabled: Auditing and Diagnostics. Auditing tracks a set of database events like queries and login attempts. Diagnostics will make copies of a specific set of system DMVs periodically which can be helpful because Synapse only stores a few thousand records depending on the DMV.

Everything from here assumes that you have a Log Analytics workspace created and a Synapse Workspace with a Gen 2 dedicated SQL pool deployed.

# Enabling Auditing

Auditing can be enabled at the workspace level, which will cover all databases on the workspace automatically, or at the individual database level.

First, navigate to your Synapse Analytics Workspace or dedicated SQL pool in the Azure Portal. My screenshots will show the configuration from the dedicated SQL pool, but the same setting can be found under the label of Azure SQL Auditing at the workspace level. From here, select Auditing from the Security section.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-001.png)

Next, toggle the **Enable Azure SQL Auditing** to the on position. Next, check the boxes for the locations where you would like the log to be written, in this example we are going to focus on Log Analytics. Select a log analytics workspace to which the data will be written. Click **Save** once complete.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-002.png)

# Enabling Diagnostics

While Auditing can be turned on for a database by either enabling it at the workspace or database level, diagnostics must be enabled at the database level. There are diagnostics at the workspace level, but those are different metrics and do not apply to monitoring the dedicated SQL pool.

If you’re not there already, navigate in the Azure Portal to your dedicated SQL pool. Select **Diagnostic Settings** from the **Monitoring** section.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-003.png)

Next, click the **Add diagnostic setting** button.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-004.png)

Provide a name for this diagnostic setting. Check the box next to **Send to Log Analytics workspace**. Select the subscription and workspace to which the DMVs will be written. Select which DMVs you want to log. Finally, click Save.

Remember, the diagnostic settings each correspond to a different DMV that will be copied to your Log Analytics workspace. If you need additional DMVs you’ll need to roll your own auditing solution at this time.

| Diagnostic Setting Label | System DMV |
| --- | --- |
| SqlRequests | sys.dm\_pdw\_sql\_requests |
| RequestSteps | sys.dm\_pdw\_request\_steps |
| ExecRequests | sys.dm\_pdw\_exec\_requests |
| DmsWorkers | sys.dm\_dms\_workers |
| Waits | sys.dm\_pdw\_waits |

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-005.png)

# Reading the Logs

Data will take several minutes to show up in Log Analytics. I’ve had the initial population take up to 30 minutes. After that the data should show up much more quickly, within several minutes. Connect to your database and run a query or two to ensure we have some user queries show up in the logs.

For my example I have run the following query:  
SELECT COUNT(\*), GETDATE(), DB\_NAME() FROM IMDB.stage\_Person

To view the logs, you can navigate to your log analytics workspace, or select **Logs** from your dedicated SQL pool’s **Monitoring** section. This can be found directly beneath the Diagnostic Settings option used in the last section.

## Viewing the Audit Log

Within the Log Analytics Workspace you will see a group of tables with the prefix Synapse\* and at least one table prefixed with SQL\*. More tables may appear if you are using auditing for Azure SQL Database. The audit setup in the first section of this post will be logged to the SQLSecurityAuditEvents table.

This sample query pulls a few of the relevant columns from the SQLSecurityAuditEvents table.

```sql
SQLSecurityAuditEvents
| project
    ResourceGroup,
    LogicalServerName,
    DatabaseName,
    EventTime,
    Category,
    ActionName,
    IsServerLevelAudit,
    Succeeded,
    tostring(SessionId),
    ClientIp,
    HostName,
    ServerPrincipalName,
    DurationMs,
    Statement,
    TenantId,
    _ResourceId
```

Here you can see the query I ran in the log with an action of BATCH COMPLETED.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-006.png)

## Viewing the Diagnostic Logs (DMVs)

Where all audit events are logged to the same table, regardless of the event type, each of the DMVs enabled in Diagnostic settings gets logged to its own table. Expanding on the list of DMVs from earlier in the post we can see the Log Analytics table closely matches the diagnostic setting name with a prefix of SynapseSqlPool.

| Diagnostic Setting Label | System DMV | Log Analytics Table |
| --- | --- | --- |
| SqlRequests | sys.dm\_pdw\_sql\_requests | SynapseSqlPoolSqlRequests |
| RequestSteps | sys.dm\_pdw\_request\_steps | SynapseSqlPoolRequestSteps |
| ExecRequests | sys.dm\_pdw\_exec\_requests | SynapseSqlPoolExecRequests |
| DmsWorkers | sys.dm\_dms\_workers | SynapseSqlPoolDmsWorkers |
| Waits | sys.dm\_pdw\_waits | SynapseSqlPoolWaits |

This sample query pulls a few of the relevant columns from the SynapseSqlPoolExecRequests table.

```sql
SynapseSqlPoolExecRequests
| summarize
	TimeGenerated = max(TimeGenerated),
	ResourceGroup = any(ResourceGroup),
	LogicalServerName = any(LogicalServerName),
	DatabaseId = any(toreal(DatabaseId)),
	StartTime = max(StartTime),
	EndCompileTime = max(EndCompileTime),
	Category = any(Category),
	Status = min(Status),
	Command = any(Command)
	by RequestId
```

Here you can see the query I ran in the SynapseSqlPoolExecRequests table.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-azure-sunapse-analytics-007.png)

# Wrapping it up

There is a lot more that can be accomplished with KQL queries in Log Analytics. You may even take the queries found in the accompanying [Dedicate SQL Pools (Formerly SQL DW)](https://bradleyschacht.com/log-analytics-with-dedicated-sql-pools-formerly-sql-dw/) post and combine them to give a single view of all your dedicate SQL pools.

The key take learning from this walkthrough is:

1. Auditing and Diagnostics are different options and enabled in different locations.
2. Auditing writes all the data to a single table no matter the action.
3. Diagnostics pull a few key DMVs from Synapse and write them to the log. Each DMV is written to its own table in Log Analytics.
4. Something not discussed is that there is the potential on highly used systems for some audit or diagnostic records could be missed.
