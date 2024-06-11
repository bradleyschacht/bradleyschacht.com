---
title: "Log Analytics with Dedicated SQL Pools (Formerly SQL DW)"
date: "2021-11-01"
categories: 
  - "azure"
  - "syndication"
---

There are a lot of services in Azure. Way more than a few. What is something you want to do with all your services and applications? You want to monitor them. How do you do that? By looking at the logs that are produced. How do you capture and make sense of thousands of log entries from hundreds of services for this really awesome solution you’ve been working on? Azure Monitor.

This post isn’t about Azure Monitor though. Not directly anyway.

Azure Synapse is no different than any other application or service in your environment. It serves a purpose in the architecture and as a result needs special attention from people with a specific skillset, in this case DBAs and developers. That’s where Azure Monitor comes into the picture and what we will be discussing today: how to make sense of the integration between auditing in Dedicated SQL Pools (Formerly SQL DW) and Azure Monitor. The focus will be on Dedicated SQL Pools (Formerly SQL DW) not Azure Synapse Analytics, but more on that in a moment.

First, those with an eye for detail will notice that this post has Log Analytics in the title and not Azure Monitor. That’s true. A very brief explanation so everyone has things straight in their heads. I put Log Analytics in the title because when you set up auditing or diagnostics the option is labeled “Send to Log Analytics workspace” not “Send to Azure Monitor” and I wanted people to be able to find this post. Azure Monitor is a set of functionalities for collecting and analyzing logs. There are some prebuilt integrations and visualizations with some Azure services like Key Vault or Storage Accounts Then there is Log Analytics, a functionality inside Azure Monitor which also happens to be where Synapse writes its logs, for storing and querying all log data. Queries are written in a Kusto Query Language or KQL.

Second, let’s be sure a few very important details are clear. When referring to Azure Synapse there are two distinct but very similar services. They are both the same Microsoft cloud MPP database engine, both cover the same general T-SQL surface area, both scale in terms of DWUs, both talk about “Azure Synapse Analytics” in the documentation and description, but one is deployed to an Azure SQL Server, and one is deployed to a Workspace.

We need to be very clear about this because they both operate slightly different. Again, this post is focused on Dedicated SQL Pools (Formerly SQL DW). An alternate post is available for [Azure Synapse Analytics Dedicated SQL Pools (Gen 2)](https://bradleyschacht.com/log-analytics-with-azure-synapse-analytics/) if you need information about that deployment option.

Let’s get started.

# What are my options?

There are two types of logging that can be enabled: Auditing and Diagnostics. Auditing tracks a set of database events like queries and login attempts. Diagnostics will make copies of a specific set of system DMVs periodically which can be helpful because Synapse only stores a few thousand records depending on the DMV.

Everything from here assumes that you have a Log Analytics workspace created and a Dedicated SQL Pools (Formerly SQL DW) deployed.

# Enabling Auditing

Auditing can be enabled at the logical server level, which will cover all databases on the logical server automatically, or at the individual database level.

First, navigate to your logical SQL server or dedicated SQL pool in the Azure Portal. My screenshots will show the configuration from the dedicated SQL pool, but the same setting can be found under the label of Auditing at the logical SQL server level. From here, select Auditing from the Security section.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-001.png)

Next, toggle the **Enable Azure SQL Auditing** to the on position. Next, check the boxes for the locations where you would like the log to be written, in this example we are going to focus on Log Analytics. Select a log analytics workspace to which the data will be written. Click **Save** once complete.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-002-1.png)

# Enabling Diagnostics

While Auditing can be turned on for a database by either enabling it at the logical SQL server or database level, diagnostics must be enabled at the database level. There are no diagnostics at the logical SQL server level.

If you’re not there already, navigate in the Azure Portal to your dedicated SQL pool. Select **Diagnostic Settings** from the **Monitoring** section.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-003.png)

Next, click the **Add diagnostic setting** button.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-004.png)

Provide a name for this diagnostic setting. Check the box next to **Send to Log Analytics workspace**. Select the subscription and workspace to which the DMVs will be written. Select which DMVs you want to log. There is an additional SQLSecurityAuditEvents entry in my screenshot which is there because I already enabled the database audit. We'll ignore that for the time being. Finally, click Save.

Remember, the diagnostic settings each correspond to a different DMV that will be copied to your Log Analytics workspace. If you need additional DMVs you’ll need to roll your own auditing solution at this time.

| Diagnostic Setting Label | System DMV |
| --- | --- |
| SqlRequests | sys.dm\_pdw\_sql\_requests |
| RequestSteps | sys.dm\_pdw\_request\_steps |
| ExecRequests | sys.dm\_pdw\_exec\_requests |
| DmsWorkers | sys.dm\_dms\_workers |
| Waits | sys.dm\_pdw\_waits |

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-005.png)

# Reading the Logs

Data will take several minutes to show up in Log Analytics. I’ve had the initial population take up to 30 minutes. After that the data should show up much more quickly, within several minutes. Connect to your database and run a query or two to ensure we have some user queries show up in the logs.

For my example I have run the following query:  
SELECT \*, GETDATE(), DB\_NAME() FROM dbo.MyTable

To view the logs, you can navigate to your log analytics workspace, or select **Logs** from your dedicated SQL pool’s **Monitoring** section. This can be found directly beneath the Diagnostic Settings option used in the last section.

## Viewing the Audit Log

Within the Log Analytics Workspace you may see a group of tables with the prefix Synapse\*. These tables correspond to diagnostic settings configured on the Synapse Analytics Workspaces. If you do not have any Synapse Workspaces these tables will likely not show up. More tables may appear if you are using auditing for Azure SQL Database. The audit setup in the first section of this post will be logged to the AzureDiagnostics table.

This sample query pulls a few of the relevant columns from the AzureDiagnostics table by narrowing down the data to the SQLSecurityAuditEvents category which is used for the Audit log. For Synapse Workspace Dedicated SQL Pools, as opposed to the "formerly SQL DW" SQL Pools that are the focus of this post, the data is written to a separate table called SQLSecurityAuditEvents rather than being a category of log within the AzureDiagnostics table.

```sql
AzureDiagnostics
| where Category == "SQLSecurityAuditEvents"
| project
        ResourceGroup,
        LogicalServerName_s,
        database_name_s,
        event_time_t,
        Category,
        action_name_s,
        tobool(is_server_level_audit_s),
        tobool(succeeded_s),
        tostring(session_id_d),
        client_ip_s,
        host_name_s,
        server_principal_name_s,
        tolong(duration_milliseconds_d),
        statement_s,
        TenantId,
        _ResourceId
| project-rename
        ResourceGroup,
        LogicalServerName=LogicalServerName_s,
        DatabaseName=database_name_s,
        EventTime=event_time_t,
        Category,
        ActionName=action_name_s,
        IsServerLevelAudit=is_server_level_audit_s,
        Succeeded=succeeded_s,
        SessionId=session_id_d,
        ClientIp=client_ip_s,
        HostName=host_name_s,
        ServerPrincipalName=server_principal_name_s,
        DurationMs=duration_milliseconds_d,
        Statement=statement_s,
        TenantId,
        _ResourceId
```

Here you can see the query I ran in the log with an action of BATCH COMPLETED.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-006.png)

## Viewing the Diagnostic Logs (DMVs)

Where Synapse Workspaces write DMV data to separate tables, for Dedicated SQL Pools (Formerly SQL DW) each of the DMVs enabled in Diagnostic settings gets logged to the same AzureDiagnostics table used in the Auditing section above, but with a category that corresponds to the diagnostic setting/System DMV.

| Diagnostic Setting Label | System DMV | Log Analytics AzureDiagnostics Category |
| --- | --- | --- |
| SqlRequests | sys.dm\_pdw\_sql\_requests | SqlRequests |
| RequestSteps | sys.dm\_pdw\_request\_steps | RequestSteps |
| ExecRequests | sys.dm\_pdw\_exec\_requests | ExecRequests |
| DmsWorkers | sys.dm\_dms\_workers | DmsWorkers |
| Waits | sys.dm\_pdw\_waits | Waits |

This sample query pulls a few of the relevant columns from the AzureDiagnostics table by narrowing the data down to the ExecRequests Category. This data corresponds to the system DMV sys.dm\_pdw\_exec\_requests.

```sql
AzureDiagnostics
| where Category == "ExecRequests"
| project
	ResourceGroup,
	LogicalServerName_s,
	DatabaseId_d,
	StartTime_t,
	EndCompileTime_t,
	Category,
	Status_s,
	RequestId_s,
	Command_s
| summarize
	ResourceGroup = any(ResourceGroup),
	LogicalServerName = any(LogicalServerName_s),
	DatabaseId = any(DatabaseId_d),
	StartTime = max(StartTime_t),
	EndCompileTime = max(EndCompileTime_t),
	Category = any(Category),
	Status = min(Status_s),
	Command = any(Command_s),
	record_count = count()
	by RequestId_s
```

Here you can see the query I ran focused on the ExecRequests category.

![](https://images.bradleyschacht.com/wp-content/uploads/2021/11/log-analytics-with-dedicated-sql-pools-formerly-sql-dw-007.png)

# Wrapping it up

There is a lot more that can be accomplished with KQL queries in Log Analytics. You may even take the queries found in the accompanying [Azure Synapse Analytics Dedicated SQL Pools (Gen 2)](https://bradleyschacht.com/log-analytics-with-azure-synapse-analytics/) post and combine them to give a single view of all your dedicate SQL pools.

The key take learning from this walkthrough is:

1. Auditing and Diagnostics are different options and enabled in different locations.
2. Auditing writes all the data to a single table no matter the action.
3. Diagnostics pull a few key DMVs from Synapse and write them to the log. Each DMV is written to the same table in Log Analytics each with a different category.
4. Something not discussed is that there is the potential on highly used systems for some audit or diagnostic records could be missed.
