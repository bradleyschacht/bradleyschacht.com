---
title: "Collecting Server Performance Metrics: PowerShell"
date: "2019-07-30"
categories: 
  - "sql-server"
  - "syndication"
  - "windows"
---

In a recent post I wrote about [collecting server performance metrics using Performance Monitor](https://bradleyschacht.com/collecting-server-performance-metrics-performance-monitor/), a free utility built into Windows. With a little work up front, we are able to collect metrics for minutes, hours, or even days to gain insights into how our servers are being used.

I often use this to evaluate SQL Servers that my customers are migrating to Azure. It helps me figure out if their current servers are sized properly or if we can reduce the size during the migration to right-size the solution and help save on cost.

Today, we are going to look at how to collect server performance metrics using another solution: PowerShell.

Let's review what we will be collecting. For a little more in-depth discussion on each of these areas visit [the original post](https://bradleyschacht.com/collecting-server-performance-metrics-performance-monitor/).

- CPU
    - Processor – % Processor Time – Total CPU usage percentage
- Memory
    - Memory – Available MBytes – Amount of free memory (careful reading into this SQL takes and holds memory)
    - Paging File – % Usage – Memory swap to disk
- Disk (collect for each logical disk)
    - Logical Disk – Avg. Disk Bytes/Read – Size of reads
    - Logical Disk – Avg. Disk Bytes/Write – Size of writes
    - Logical Disk – Avg. Disk sec/Read – Latency for reads
    - Logical Disk – Avg. Disk sec/Write – Latency for writes
    - Logical Disk – Disk Read Bytes/sec – Throughput for reads
    - Logical Disk – Disk Write Bytes/sec – Throughput for writes
    - Logical Disk – Disk Reads/sec – IOPS for reads
    - Logical Disk – Disk Writes/sec – IOPS for writes
- Network Bandwidth
    - Network Interface – Bytes Total/sec – Total network in and out

Let's get started.

**Collecting Performance Counters with Get-Counter**

Accessing Windows performance counters is accomplished by using the Get-Counter cmdlet. There are a few interesting ways to use this cmdlet that provide some great flexibility. Here is the general syntax with some parameters that I find useful.

```powershell
Get-Content -Counter "CounterNameHere" -ComputerName "ComputerNameHere" -SampleInterval 15 -MaxSamples 5
```

- Counter is the performance counter to collect. You can also specify a list of counters, which I will show later in this post.
- ComputerName will allow you to collect counters from a remote computer. If this is not specified the collection will happen locally. Be sure you have the proper permissions on the remote computer.
- SampleInterval will set the number of seconds between sampling the counters. If this is not specified the default behavior is every second.
- MaxSamples will set the number of samples to be gathered. If this is not specified only one sample is collected. As an alternative, you can specify the parameter "Continuous" and the collection will continue until the script is terminated.

Here is how this looks in action when collecting the Processor % Time from one of the SQL Servers on my network. For simplicity in the screenshot I left off some parameters and only collected one sample.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-powershell-001.png)

The format of the counter name is pretty intuitive, but what if you wanted a list of the counters? You could go over to Performance Monitor and get them but that requires a lot of work. You'd have to open the tool, find the counters, add them, then change the view to see the counter names or copy them over to something like Notepad to see the exact name. Or...we could use PowerShell to get a list of all the counters!

Using the ListSet parameter we can view the counters for any category. In this example we see the processor counters.

```powershell
Get-Counter -ListSet "Processor"
```

Let's explore a little. To start, you'll see the output from when using ListSet. It shows some information about the counter as well as the available counters and the instance of each counter. I have 4 cores on this laptop, so the instances will for the processor will show 0 through 3 to represent each core in addition to the \_Total instance that aggregates all the cores. In the screenshot you'll see I listed each of the counters that are available so I can make a decision on which counter I would like.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-powershell-002.png)

Instead of Processor you can also specify Memory, LogicalDisk (without a space), or network interface (with a space) for the categories we discussed in this and the previous post. If you want to see all the counters available then use \* rather than specifying processor, memory, or some other category in the list set. Make sure to pipe that output to a file or store it in a variable though, there will be a lot of results and it won't be easy to locate the relevant information inside the PowerShell window.

**Dealing with Multiple Instances**

There are times when we want an aggregate counter value and times when we need the counter value for each instance. For the processor I don't really need to know what each core is doing, so I will use the counter \\Processor(\_Total)\\% Processor Time. On the disk though, I would like to know what is happening on each individual disk, not just the aggregate total. I'm interested in what happens on the OS disk vs. the disk holding my SQL Server data and log files. Just getting the total doesn't do me much good. This is where wildcards come into play. Sure, we could specify to collect the Disk Reads/sec for the S: drive that stores my data files. What happens on the next server though where I don't have an S: drive, but instead I have an M: drive?

We could write some logic to list the disks on a given server and dynamically build this counter list. That seems like a lot of work though and I'm not quite that invested in writing this code tonight. Thankfully, there is a solution. Instead of specifying a drive letter, we'll use the asterisk that we saw in the previous counter list output. This will provide the output for each drive individually as well as a \_Total.

```powershell
Get-Counter -Counter "\LogicalDisk(S:)\Disk Reads/sec" -ComputerName "BS-SRV-SQL01"
Get-Counter -Counter "\LogicalDisk(*)\Disk Reads/sec" -ComputerName "BS-SRV-SQL01"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/collecting-server-performance-metrics-powershell-003.png)

Just trust me, that's working and the 0's are correct. It's late, I'm sitting in a hotel room and I didn't simulate a load on the server. It's just sitting there. Doing absolutely nothing right now. The important thing is I can see counter values for each disk, not just the ones I specify which is good, because until I ran this and saw the output I completely forgot that I added another data disk, the T: drive, to that VM to handle loading a few hundred GB of NYC Taxi data to update a demo dataset that I built out. I completely would have missed that if I were listing drives manually.

**Putting it All Together**

Now we can get individual performance counters with PowerShell, how about we put together a little script to collect these counters much like we did in the last post. Collect a specific set of counters every 15 seconds and we'll let it run for a day or two. Feel free to use the script below and adapt it for your needs.

A few notes on the script:

1. Specify a directory where the result file will be stored. Be sure you have permissions to this directory and PowerShell is running as administrator if necessary. There is logic to check if this directory exists, and if not, it will be created.
2. Specify the computer from which you wish to collect the performance counters. Leave this blank to collect from the computer where the script is running.
3. Set the interval at which to collect the counters. I set this to 15 seconds just like Performance Monitor in the last post.
4. Set the number of samples. This will be the number of samples taken at the interval specified. 240 samples at 15 second intervals is 1 hour of data collected.
5. The file the data will be written to is placed in the output directory with the name of **Computer Date Time.csv**
6. I set the variables for the Get-Counter cmdlet using splatting because it was easier than dynamically creating a string with the proper parameters that I would then have to execute. This helps me add the proper parameters based on the input at the beginning of the script (continuous and maxsamples can't both be specified).

```powershell
cls
$outputDirectory = "C:\Users\bschacht\Desktop\Performance Counters" #Directory where the restult file will be stored.
$computerName = "" #Set the Computer from which to collect counters. Leave blank for local computer.
$sampleInterval = 15 #Collection interval in seconds.
$maxSamples = 240 #How many samples should be collected at the interval specified. Set to 0 for continuous collection.
#Check to see if the output directory exists. If not, create it.
if (-not(Test-Path $outputDirectory))
    {
        Write-Host "Output directory does not exist. Directory will be created."
        $null = New-Item -Path $outputDirectory -ItemType "Directory"
        Write-Host "Output directory created."
    }
#Strip the \ off the end of the directory if necessary.
if ($outputDirectory.EndsWith("\")) {$outputDirectory = $outputDirectory.Substring(0, $outputDirectory.Length - 1)}
#Create the name of the output file in the format of "computer date time.csv".
$outputFile = "$outputDirectory\$(if($computerName -eq ''){$env:COMPUTERNAME} else {$computerName}) $(Get-Date -Format "yyyy_MM_dd HH_mm_ss").csv"
#Write the parameters to the screen.
Write-Host "
Collecting counters...
Press Ctrl+C to exit."
#Specify the list of performance counters to collect.
$counters =
    @(`
    "\Processor(_Total)\% Processor Time" `
    ,"\Memory\Available MBytes" `
    ,"\Paging File(_Total)\% Usage" `
    ,"\LogicalDisk(*)\Avg. Disk Bytes/Read" `
    ,"\LogicalDisk(*)\Avg. Disk Bytes/Write" `
    ,"\LogicalDisk(*)\Avg. Disk sec/Read" `
    ,"\LogicalDisk(*)\Avg. Disk sec/Write" `
    ,"\LogicalDisk(*)\Disk Read Bytes/sec" `
    ,"\LogicalDisk(*)\Disk Write Bytes/sec" `
    ,"\LogicalDisk(*)\Disk Reads/sec" `
    ,"\LogicalDisk(*)\Disk Writes/sec"
    )
#Set the variables for the Get-Counter cmdlet.
$variables = @{
    SampleInterval = $sampleInterval
    Counter = $counters
}
#Add the computer name if it was not blank.
if ($computerName -ne "") {$variables.Add("ComputerName","$computerName")}
#Either set the sample interval or specify to collect continuous.
if ($maxSamples -eq 0) {$variables.Add("Continuous",1)}
else {$variables.Add("MaxSamples","$maxSamples")}
#Show the variables then execute the command while storing the results in a file.
$variables
Get-Counter @Variables | Export-Counter -FileFormat csv -Path $outputFile -Force
```

Go run the script, grab the output file(s), fire up Power BI or Excel and start analyzing!

If you want to see a sample of my Power BI report head over to [last week's post](https://bradleyschacht.com/collecting-server-performance-metrics-performance-monitor/) and scroll down to the very end.
