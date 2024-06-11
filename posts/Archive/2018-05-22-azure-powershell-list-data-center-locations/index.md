---
title: "Azure PowerShell - List Data Center Locations"
date: "2018-05-22"
categories: 
  - "azure"
  - "powershell"
  - "scripts"
  - "syndication"
---

Occasionally I will come across the need to use PowerShell for my day to day activities. One such need came up this week when I was preparing to do training with a customer. Part of that meant creating 6 data science VMs. So we updated the software on a VM and went to clone it, ran into some issues and then decided to break out the PowerShell to fix the issue. For those who use PowerShell to do anything in Azure you will know that you will often need to supply the location parameter. In the portal that's not a problem, you just select from the drop down menu. However, in PowerShell you have to know exactly what the name of the data center is and how it is formatted. Is it East US 2, EastUS2, East US2, or something else. And then you have to remember that for all the data centers. And there are a bunch of them; we have 50 regions and counting. Thankfully there is a PowerShell cmdlet that will assist with this! Let's fire up Azure PowerShell and take a look. Of course we will want to login with Connect-AzureRmAccount to get started. Then it is a simple cmdlet with no parameters necessary: Get-AzureRmLocation. You can cleanup the output by adding Format-Table.

```
Get-AzureRmLocation |Format-Table
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-data-center-locations-001.png) Take a look at the Providers property in the output.This lists the resources available in the region. This is useful for finding regions that support Azure SQL, for instance.

```
Get-AzureRmLocation | Where-Object Providers -like "*sql*" | Format-Table
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-data-center-locations-002.png) Should you decide that you just want to view all the services in a particular region, it's not too much extra work.

```
$providers = Get-AzureRmLocation | Where-Object {$_.Location -eq "eastus2"} | select Providers
$providers.Providers
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-data-center-locations-003.png) There you have it. A handy little PowerShell cmdlet that will help you figure out what to put in the location parameter for other cmdlets like creating a Virtual Machine or creating a new Azure SQL Database. Next week we'll take a look at finding the virtual machine sizes that are available in each data center and the associated parameter values.

**\*\*Update\*\* The next blog has been released: [Azure PowerShell - List Virtual Machine Sizes](https://bradleyschacht.com/azure-powershell-list-virtual-machine-sizes/)**
