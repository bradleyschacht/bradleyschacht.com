---
title: "Collecting Server Performance Metrics: Performance Monitor"
date: "2019-07-23"
categories: 
  - "sql-server"
  - "syndication"
  - "windows"
---

Whether you're a DBA/administrator looking to tune a server, planning for hardware updates, or looking to make a move to the cloud there are a few key performance metrics you're going to want to collect. How do you know if the bottleneck is memory, disk or CPU? How do you know which of the Azure VM sizes to choose? The answer to these questions and other lies in the performance metrics on your existing servers.

Sure, you could just take the number of cores and memory, go to the Azure portal and choose a VM that matches. Is that really the best way to go about a cloud migration? Not really. Are you using all the memory that's on the server? Is the CPU constantly being maxed out? How do you even know what size disk to select?

There are a few key performance metrics that you'll want to gather to answer these questions. Today we are going to look at collecting a few of those metrics using a free tool built into Windows called Performance Monitor (also referred to as Perfmon).

Ultimately we are going to collect metrics on three main areas: CPU, memory and disk (then add network bandwidth for good measure).

**CPU**

On the CPU we will be looking to get information about the compute capacity, such as the number of processors and cores as well as the utilization.

Relevant CPU counters collected at the total level:

- Processor - % Processor Time - Total CPU usage percentage

**Memory**

Like the CPU, this is pretty straightforward. How much do we have and how much are we using. Also important here is how often does data page to disk.

Relevant memory counters collected at the total level:

- Memory - Available MBytes - Amount of free memory (careful reading into this SQL takes and holds memory)
- Paging File - % Usage - Memory swap to disk

**Disk**

This is likely the area that is most complex and will generate the most discussion. Everything from "IOPS aren't a real thing" to "IOPS are the best way to measure disk performance". We are going to gather a few different pieces of information on the disk side. IOPS, latency, and throughput are all important in different ways for sizing a new system and for tuning an existing system. So let's go ahead and get some of the basics gathered. Depending on what this data will be used for, you can collect individual disk metrics or just choose the total for all disks. Collecting for individual disks makes analyzing more work, but also gives more detail.

Relevant disk counters collected for each logical disk:

- Logical Disk - Avg. Disk Bytes/Read - Size of reads
- Logical Disk - Avg. Disk Bytes/Write - Size of writes
- Logical Disk - Avg. Disk sec/Read - Latency for reads
- Logical Disk - Avg. Disk sec/Write - Latency for writes
- Logical Disk - Disk Read Bytes/sec - Throughput for reads
- Logical Disk - Disk Write Bytes/sec - Throughput for writes
- Logical Disk - Disk Reads/sec - IOPS for reads
- Logical Disk - Disk Writes/sec - IOPS for writes

**Network Bandwidth**

For a migration to Azure VMs this can be useful just to do some sanity checks on the new configuration and to help identify if slowdowns are on the Azure side or your connection from on-prem to Azure.

Relevant network counters collected at the server level:

- Network Interface - Bytes Total/sec - Total network in and out

Let's get started.

**Launching Performance Monitor**

To access the Windows Performance Monitor simply click the Start button and start typing "Performance Monitor" until it shows up in the result list. If you happen to be running an older version of Windows without search functionality or can't seem to find Performance Monitor in the results go to Run and simply type "perfmon". It is also available in the Computer Management console under System Tools, then Performance.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-001.png)

This utility will allow live viewing of metrics and background collection. We will want to setup a background collection that runs for a long enough time to get a solid representative sample of the workloads on the server. In an ideal world this would run for a few days to cover the major events like changes in daily user load and nightly batch processing.

**Setting Up the Data Collector**

There are a few ways to setup the collection of performance counters. I will outline one way in this post.

In the Performance Monitor navigation tree, expand the Data Collector Sets node. Then, right-click on the User Defined node and select New > Data Collector Set.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-002.png)

Name the data collector set and select the option labeled Create manually (Advanced).

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-003.png)

When asked for the type of data to include make sure to check the box next to Performance counter otherwise there are going to be a few extra steps to get the counters into the collector and the next few steps won't match up at all.

Now we are at the good part. Which performance counters would you like to log? Click the add button and refer to the list above for which counters to add. Let's keep the sample interval at 15 seconds. Click OK when all the counters are added. Depending on the number of disks there could be quite a large list.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-004.png)

After being returned to the counter summary, click Next.

Tell Performance Monitor where to save the results. Click Next.

On the final page you can change the account that will be used for data collection. Be sure that this account has access to gather the metrics from this computer or on the remote computer if you are setting this up to collection from another location. Keep the radio button on save and close, then click Finish.

You will be returned to the Performance Monitor console. We need to make a small change to the way the data is stored, as we will want it in a CSV format. Let's navigate to the collector. On the navigation tree expand the User Defined node and select the collector with the name specified in the earlier steps. There should be one collector there, likely named DataCollector01 because Performance Monitor is as creative as I am. Right-click on that collector and open the properties.

Note: If you want to control the name of the collector, rather than telling the wizard we wanted to add performance counters, you would leave that option blank. The wizard would end rather than allowing you to add performance counters. Then you'd right-click the data collector set and select New > Data Collector. The first page of that wizard allows for a custom name.

On the Performance Counters tab there is an option for Log format. The default is going to be Binary. Change this to  tab or comma separated so the data is more easily consumed. You can also switch over to the File tab and change the name of this collector's file so it's more meaningful. Finally, click OK.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-005.png)

Now we are ready to start collecting!

Right-click the data collector set that was just created and select Start. The counters will be written every 15 seconds into a file in the directory specified in the collector wizard.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-006.png)

**Viewing the Results**

When you are ready to take a look at the collection results it's time to dust off your Excel or Power BI skills.

I like to bring this data into Power BI because it's easy to clean up the results and it's easy to build some quick visuals. A few clicks will help remove the first row in the results that are blank, unpivot all the columns into rows, remove my computer name from the counter text, add a few category labels, and change some column headers.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-007.png)

I can then go build a few visualizations. I have a summary showing the total, median, average, minimum and maximum for each counter, then the same calculations for each counter group.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-008.png)

I also have a page for each of the categories with some visualizations. Because who doesn't love a good line chart?

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-performance-monitor-009.png)

A table of data is great, and the details are absolutely necessary. However, it can be difficult to see the peaks and valleys in a list of counter results, especially when looking over a day or two. A simple line chart can really help with that. I generally want to see how the different data points trend over the entire collection period. I want to see how often the system runs close to the maximum. I want to see if the average is because there are sustained periods of maximum activity followed by sustained periods of no activity or if it's a constant amount of activity with some periodic peaks. All of these charts will look different based on if the server is running a transactional system or a data warehouse.

**What do I do with this information?**

I'm going to fall back on my consulting answer. It depends. If you're looking to tune the system, then this will help you know where to look. There are other pieces of information in SQL Server these days that will help point you in the right direction better than these counters.

For me, I generally use this when I'm working with customers who are looking to move servers into Azure. It's great to look at the existing server specs and just use that but it only gives part of the picture. We still need to understand the disk requirements as throughput and IOPS in Azure are not fixed values. They vary based on the VM size, disk type chosen, and the number of disks. We don't want to throw 24 cores at a server just because it has 24 today because the existing server may have changed over time. It may be that a consolidation was done and the server is now underpowered. It may be that services were moved off the server and now the CPU doesn't go above 26% even at peak times. We don't want to overpower VMs because then you're wasting money. We don't want to underpower VMs because then you're having a terrible experience.

In a future post I'll go through how to match this information up to Azure VM sizes. In the meantime, now you have a good way to gather some baseline performance metrics from your existing servers.
