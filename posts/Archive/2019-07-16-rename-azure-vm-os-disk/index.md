---
title: "Rename Azure VM OS Disk"
date: "2019-07-16"
categories: 
  - "azure"
  - "syndication"
  - "windows"
---

In a recent post about [creating Azure VMs using PowerShell](https://bradleyschacht.com/create-new-azure-vm-with-powershell/), I noted a common issue that I run into where the name of the OS disk does not conform to naming standards. The format for a VM OS disk when created using the Azure portal or when not otherwise specified through PowerShell is {VMName}\_OsDisk\_1\_{UniqueIdentifier}. For most organizations that does not meet their naming standards. For me, in my Azure sandbox, it just bothers me because it's messy.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-001.png)

How do we get from OSDiskRename\_OsDisk\_1\_ d6783bf8f6604032972883b96a78c579 to OSDiskRename-OS?

Unfortunately, it's not as simple as renaming the resource. Like many resources in Azure, a managed disk cannot be renamed. That means we have to find a creative way to go about this. In general we will use the following steps:

1. Shudown the VM.
2. Create a copy of the existing disk with the desired name.
3. Swap the OS disk on the VM.
4. Restart the VM.
5. Delete the old disk.

Luckily, this can all be done with just a few lines of PowerShell. They key pieces are the cmdlets New-AzDisk and Set-AzVmOsDisk.

With New-AzDisk we will specify the configuration for the new OS disk, including the source disk to copy. We can also modify the size (but this size must be greater than or equal to the current disk size), and the performance Sku for the disk. Here you can see the output from the first section of code that creates a copy of the original OS disk.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-002.png)

In the Azure portal, you can see the new disk has been create, but is not yet associated with a VM.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-003.png)

With Set-AzVmOsDisk we will specify the virtual machine on which to swap the disk, and the new disk to be set for the OS. Here you can see the output from the second section of code that changes the OS disk on the VM.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-004.png)

We can look at the VM configuration in the Azure portal to verify the change.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-005.png)

Just for good measure, I'll remote desktop to the VM to make sure it is functional.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-006.png)

Finally, there is an optional line of code to delete the original disk. If you do not include -Force a popup will ask you to confirm that you understand a resource will be removed.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-007.png)

There you have it! A swapping the OS disk is pretty easy these days. Now you can take all those virtual machines that were created in the Azure portal or without disk naming standards having been considered and clean them up.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/rename-azure-vm-os-disk-008.png)

Here is the full set of PowerShell code.

```powershell
#Define the following parameters.
$location = "EastUS2"
$resourceGroup = "OSDiskRename-RG"
$originalOsDiskName = "OSDiskRename_OsDisk_1_d6783bf8f6604032972883b96a78c579"
$newOsDiskName = "OSDisRename-OS"
$virtualMachineName = "OSDiskRename"
#Get a list of all the managed disks in a resource group.
#Get-AzDisk -ResourceGroupName $resourceGroup | Select Name
#Get the source managed disk.
$sourceDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName $originalOsDiskName
#Create the disk configuration.
$diskConfig = New-AzDiskConfig -SourceResourceId $sourceDisk.Id -Location $sourceDisk.Location -CreateOption Copy -DiskSizeGB 127 -SkuName "Premium_LRS"
#Create the new disk.
New-AzDisk -Disk $diskConfig -DiskName $newOsDiskName -ResourceGroupName $resourceGroup
#Swap the OS Disk out for the renamed disk.
$virtualMachine = Get-AzVm -ResourceGroupName $resourceGroup -Name $virtualMachineName
$newOsDisk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $newOsDiskName
Set-AzVMOSDisk -VM $virtualMachine -ManagedDiskId $newOsDisk.Id -Name $newOsDisk.Name
Update-AzVM -ResourceGroupName $resourceGroup -VM $virtualMachine
#Delete the original disk.
Remove-AzDisk -ResourceGroupName $resourceGroup -DiskName $originalOsDiskName -Force
```
