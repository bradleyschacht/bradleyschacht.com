---
title: "Azure PowerShell - List Virtual Machine Sizes"
date: "2018-05-29"
categories: 
  - "azure"
  - "powershell"
  - "scripts"
  - "syndication"
---

For those who use PowerShell to do things in Azure you will know that occasionally there is a parameter that you need to get right but are unsure of the exact value that should be there. In the [Azure Portal](http://portal.azure.com) it's easy, you pick from a drop down menu where the required, pre-defined, values are and enter into the text box where it's a free form value. Last week I wrote about using [PowerShell to get a list of all the data center values](https://bradleyschacht.com/azure-powershell-list-data-center-locations/). So when you have to chose a location you know if the parameter value should be EastUS or East US. This week we look at another cmdlet that will get us a list of valid options, this time to get a list of virtual machine sizes. For the cmdlet to create a new virtual machine configuration asks for the size. But what do I put in there? Big? Large? DS3\_v2? 8 Cores and some memory please?

```
New-AzureRmVMConfig -VMName "BradVM" -VMSize "HELP ME PLEASE"
```

Let’s fire up Azure PowerShell and take a look. Of course we will want to login with Connect-AzureRmAccount to get started. The cmdlet Get-AzureRmVmSize will list all the sizes, except it wants a parameter...the location. Why does it want a parameter for the location? The answer is quite simple, different data centers have different hardware and therefore sometimes (not always) offer different virtual machine sizes. New VM sizes will roll out to anchor data centers like EastUS/EastUS2/WestUS (in the US) and then roll out to other data centers as the capacity is added to the remaining data centers. You will notice that older VM sizes, like the Basic VMs are available widely. So if you need to know the proper parameter for the location, head over to [last week's post](https://bradleyschacht.com/azure-powershell-list-data-center-locations/) or read on to see some other examples.

```
Get-AzureRmVmSize
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-virtual-machine-sizes-001.png) You can also supply that parameter at runtime.

```
Get-AzureRmVmSize -location "eastus2"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-virtual-machine-sizes-002.png) What if you don't want to go run this every time you need to look up a VM size? Once you see the options you can tell that they are pretty straightforward. Standard/Basic\_VMSize is the basic format of the name. But it's helpful to know what is available and where it is available. So I thought, why not drop these out to a file so I can easily go filter by VM size or data center. I often have to figure out what VM size to recommend for customers and it is helpful to know what is available in the data center(s) that they are using. A few lines of PowerShell later and we are good to go! What I really want is the output from the Get-AzureRmVmSize cmdlet but with the location added to it. Having the list dropped to a file doesn't do me any good if I don't know what data center each section ties back to. Also, I don't want a bunch of duplicate headers in my file, so I'll want to make sure however I do this that I only get one set of headers. A few searches and trial and error later I had this piece of magic. (I don't do a lot of PowerShell, so there may well be a better way of doing this, but it worked and was more clean than the first 2 versions I had hacked together, so this is what the world gets!) Most of the time we only care about a couple of data centers. Let's say that you are building your main location in East US and East US 2 but you want to do DR in West US. You can use the first line of this script to filter down to just the data centers that you care about. Or you can comment out line 1 and un-comment line 2 to get the entire list. Just be aware that if you do this for all the locations it may take a little while. I also have a couple of lines at the beginning go reset some things as I was running the code over and over. If you are doing a run-once situation then those first two lines from my screenshot will be unnecessary. \[code language="powershell"\]$locations = Get-AzureRmLocation | Where-Object {$\_.location -in "eastus2","eastus"} #$locations = Get-AzureRmLocation foreach ($l in $locations) { $vmSizes += Get-AzureRmVmSize -Location $l.Location | Select-Object @{Name="LocationDisplayName"; Expression = {$l.DisplayName}},@{Name="Location"; Expression = {$l.Location}},Name,NumberOfCores,MemoryInMB,MaxDataDiskCount,OSDiskSizeInMB,ResourceDiskSizeInMB } $vmSizes | Export-Csv -Path "AzureVmSizes.csv" -NoTypeInformation $vmSizes | ft\[/code\] ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-virtual-machine-sizes-003.png) Now you'll end up with a CSV file that can be opened in Excel, formatted as a table and is easily searchable and can be filtered. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-virtual-machine-sizes-004.png) Want to know which data centers have those giant, fancy M128 VMs? Easy! ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/azure-powershell-list-virtual-machine-sizes-005.png) You could filter the PowerShell output as well, but this just seems like a fun little project for a Monday afternoon.
