---
title: "Create New Azure VM with PowerShell"
date: "2019-07-09"
categories: 
  - "azure"
  - "syndication"
  - "windows"
---

Every so often I set out to create new resources in my Azure subscription using the command line tools (PowerShell or CLI) just to keep up to date on the changes and improvements in the different methods. While the Azure portal is a great GUI for creating virtual machines, it definitely leaves something to be desired from a customization standpoint. I'm fairly OCD about my environment and you should be too. An organized cloud environment is a happy cloud environment.

One thing I try to embed in the mind of my customers each and every day is the need for standards. If you don't have standards for your SSIS packages, Data Factory Pipelines, SQL Server names, database objects, and in today's case, cloud resources, then you are asking for a maintenance nightmare in the future. Define your standards early and be sure to enforce them with very few exceptions (some Azure resources just can't be renamed).

That brings us to today's adventure: Virtual Machine Resource Naming.

Creating a virtual machine in the Azure portal is quick and easy. Fill in a few fields, click Create, and you have a VM ready to go in a few minutes!

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/create-new-azure-vm-with-powershell-001.png)

Unfortunately, a few minutes later is when the problems arise. My OCD goes bonkers when I see what has been created. My beautiful, new VM has the right name but everything else is a garbage heap of nonsense!

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/create-new-azure-vm-with-powershell-002.png)

Some of those names are...ok...but some of them are just plain bad. What is going on with the name of that managed disk? VMBlog\_OsDisk\_1\_533318a6b0b84fdb98a00c67d1bdd0ff. The first part of that makes sense but the last part definitely doesn't. Same with the network interface. What is that 418 at the end good for?

A year and a half ago this is how my process to fix all this would have gone.

1. Create the NIC (with the name I want)
2. Create the IP address (with the name I want)
3. Create the VM
4. Copy the VHD to be a name that's not stupid
5. Destroy the VM (this and the next steps is no longer necessary, the ability to [swap managed disks](https://azure.microsoft.com/en-us/blog/os-disk-swap-managed-disks/) was added in April 2018)
6. Recreate the VM from the VHD image with the correct name
7. Associate the new VM with the NIC that has the proper naming conventions
8. Remove the VHD with the dumb name

That's not exactly the easiest path to proper naming standards. Thankfully, today the process is much easier. After finding the right combination of PowerShell cmdlets I was able to get a pretty streamlined script down that I will share below.

To use this script you will need to already have a resource group and virtual network created or extend the script to add those items as well. The script is pretty basic, but feel free to use it and expand it as you see fit. I've taken pieces of code from several different Azure documentation pages and added them to my own code to get to this point, it's by no means 100% my brainchild. A few good extensions to this script would be additional data disk and an NSG. For my test environment though, this gets me where I need to be. Maybe in the future I'll expand the script some more and update it here.

```powershell
#Install the Az module if you haven't done so already.
#Install-Module Az
#Login to your Azure account.
#Login-AzAccount
#Define the following parameters for the virtual machine.
$vmAdminUsername = "LocalAdminUserNameHere"
$vmAdminPassword = ConvertTo-SecureString "LocalAdminP@sswordHere" -AsPlainText -Force
$vmComputerName = "BS-SRV-SQL02"
#Define the following parameters for the Azure resources.
$azureLocation              = "EastUS2"
$azureResourceGroup         = "BSDomain-RG"
$azureVmName                = "BS-SRV-SQL02"
$azureVmOsDiskName          = "BS-SRV-SQL02-OS"
$azureVmSize                = "Standard_E4s_v3"
#Define the networking information.
$azureNicName               = "BS-SRV-SQL02-NIC"
$azurePublicIpName          = "BS-SRV-SQL02-IP"
#Define the existing VNet information.
$azureVnetName              = "BSDomain-Vnet"
$azureVnetSubnetName        = "default"
#Define the VM marketplace image details.
$azureVmPublisherName = "MicrosoftWindowsServer"
$azureVmOffer = "WindowsServer"
$azureVmSkus = "2019-Datacenter"
#Get the subnet details for the specified virtual network + subnet combination.
$azureVnetSubnet = (Get-AzVirtualNetwork -Name $azureVnetName -ResourceGroupName $azureResourceGroup).Subnets | Where-Object {$_.Name -eq $azureVnetSubnetName}
#Create the public IP address.
$azurePublicIp = New-AzPublicIpAddress -Name $azurePublicIpName -ResourceGroupName $azureResourceGroup -Location $azureLocation -AllocationMethod Dynamic
#Create the NIC and associate the public IpAddress.
$azureNIC = New-AzNetworkInterface -Name $azureNicName -ResourceGroupName $azureResourceGroup -Location $azureLocation -SubnetId $azureVnetSubnet.Id -PublicIpAddressId $azurePublicIp.Id
#Store the credentials for the local admin account.
$vmCredential = New-Object System.Management.Automation.PSCredential ($vmAdminUsername, $vmAdminPassword)
#Define the parameters for the new virtual machine.
$VirtualMachine = New-AzVMConfig -VMName $azureVmName -VMSize $azureVmSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $vmComputerName -Credential $vmCredential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $azureNIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $azureVmPublisherName -Offer $azureVmOffer -Skus $azureVmSkus -Version "latest"
$VirtualMachine = Set-AzVMBootDiagnostic -VM $VirtualMachine -Disable
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -StorageAccountType "Premium_LRS" -Caching ReadWrite -Name $azureVmOsDiskName -CreateOption FromImage
#Create the virtual machine.
New-AzVM -ResourceGroupName $azureResourceGroup -Location $azureLocation -VM $VirtualMachine -Verbose
```

After the script finishes I have a VM, NIC, IP address, and OS disk all with exactly the names I desire. My OCD is satisfied, and my Azure environment is organized and conforms to all my standards!

Check out the new resources in the screenshot below.

- Virtual Machine: BS-SRV-SQL02
- Virtual Machine OS Disk: BS-SRV-SQL02-OS
- Network Interface: BS-SRV-SQL02-NIC
- IP Address: BS-SRV-SQL02-IP

![](https://images.bradleyschacht.com/wp-content/uploads/2019/07/create-new-azure-vm-with-powershell-003.png)

Update: If you want to add a data disk to the VM you can use the code snippet below.

```powershell
#Define the following parameters for the Azure resources. Add this to the "#Define the following parameters for the Azure resources." code section.
$azureVmDataDisk01Name      = "BS-SRV-SQL01-Data01"
#Optionally, add an additional data disk. Add this to the "#Define the parameters for the new virtual machine." code section.
$vmDataDisk01Config = New-AzDiskConfig -SkuName Standard_LRS -Location $azureLocation -CreateOption Empty -DiskSizeGB 127
$vmDataDisk01 = New-AzDisk -DiskName $azureVmDataDisk01Name -Disk $vmDataDisk01Config -ResourceGroupName $azureResourceGroup
$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $azureVmDataDisk01Name -CreateOption Attach -ManagedDiskId $vmDataDisk01.Id -Lun 0
```
