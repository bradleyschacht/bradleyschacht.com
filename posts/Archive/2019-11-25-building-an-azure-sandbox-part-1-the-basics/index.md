---
title: "Building an Azure Sandbox - Part 1 - The Basics"
date: "2019-11-25"
categories: 
  - "azure"
  - "powershell"
  - "scripts"
  - "syndication"
---

I recently posted that I was doing my [annual Azure sandbox rebuild](https://bradleyschacht.com/building-an-azure-sandbox/) but this time I wanted to do things a little differently...all in PowerShell. Unlike previous years when I would remove most of the resources, this year I removed all of the resources. As in I log into the Azure Portal and it says no resources to display. No databases, no networks, not even an empty resource group.

As I work through this process I am going to post the scripts that I use. These are by no means meant to take back and use to build your enterprise environment. There are many not so best practices in the scripts, lots of copy and paste where there should be variables, no logic to check if resources exist or remove if they do exist, and a bunch of things that could likely be done more efficiently.

## Getting Started

All you need to get started is a PowerShell window. I'm using [Visual Studio Code](https://bradleyschacht.com/using-visual-studio-code/) but this could just as easily been done with the PowerShell ISE. Visual Studio Code is a great cross platform tool that can be [downloaded here](http://aka.ms/vscode).

Once your favorite code editor is open it's time to install the Az module which is used for interacting with Azure. If you've used PowerShell for Azure in the past you may remember the AzureRM (Azure Resource Manager) module. This is the next evolution of the AzureRM module which has been deprecated. Make sure you are running your application as an administrator and run Install-Module Az.

```powershell
Install-Module Az
```

The Az module cmdlets follow a pretty general format.

- **Get-Something** will pull back all of that particular resource. For instance, Get-ResourceGroup will list all the resource groups. Get-AzVirtualNetwork will return all the virtual networks. It's an easy way to validate what you have just created or modified. Most of these commands are going to require you to specify at least a resource group.
- **New-Something** will create a new resource. For instance, New-AzVirtualNetwork will create a new virtual network. Almost all of these commands require a name for the resource, the location to which the resource will be deployed (Azure region), and the resource group in which to create the resource.
- **Remove-Something** will remove the specified resource. Again, most of these commands will require at least a name and a resource group to be specified.
- **Set-Something** will apply a change to a resource. For instance, Set-AzNetworkSecurityGroup will apply a change made to an NSG object. The change isn't actually committed and reflected in the resource until the Set command is run.

Next we will connect to our Azure account and tell our session which subscription (assuming there is more than one available) to use. We will log in, list the subscriptions, the current context, and if necessary, change subscriptions.

```powershell
#Connect to Azure account.
Login-AzAccount
#List all the subscriptions and see the current subscription context.
Get-AzSubscription
Get-AzContext
#Set the context to the appropriate subscription.
Select-AzSubscription -SubscriptionId "########-####-####-####-#########" | Set-AzContext
```

![](https://images.bradleyschacht.com/wp-content/uploads/2019/11/building-an-azure-sandbox-part-1-the-basics-001.png)

## Let's Break Some Stuff

What kind of learning exercise would this be if we didn't try to break some things. Just for the heck of it, let's unregister all of the providers. Providers tell us what resources we are able to spin up inside a given subscription. It's basically like a master switch at the subscription level for enabling and disabling different Azure features. For instance, if you don't register the Microsoft.DataFactory resource provider then you won't be able to create a new Azure Data Factory. The options will still show up in the portal, it will just give an error if you try to create one.

```powershell
#Just because I like to break things to see what happens. Let's unregister all the resource providers.
Get-AzResourceProvider | Unregister-AzResourceProvider
#Interestingly enough, there were a handful that I was unable to disable.
Get-AzResourceProvider | Format-Table
```

Fortunately and unfortunately this didn't quite break things. I was really hoping that I was going to start getting error messages when attempting to add a storage account that said I needed to go register the provider. What happens, in reality, is that if you have the permissions to enable a particular provider it will automatically register when a new provider is needed. For my little test this meant that adding new resources automatically registered the necessary providers because I'm an Owner (aka administrator) on my subscription. Test somewhat failed but still an interesting experiment. I went and looked what providers were registered after a few later commands, but in practice I kept writing code, running it, and forgetting to go check the provider list.

## Resource Groups

A vital starting point for the remainder of this series is creation of Resource Groups. These are simply logical containers where you can set restrictions if you so choose. Limit the resources that can be used, the location where they can be deployed, and set permissions that will cascade to the resources therein. We will start by setting up a few different resource groups that will be used in the future.

```powershell
#Create a few core resource groups.
New-AzResourceGroup -Name "ManagedInstance-RG" -Location "eastus2"
New-AzResourceGroup -Name "PlatformServices-RG" -Location "eastus2"
New-AzResourceGroup -Name "SqlDatabase-RG" -Location "eastus2"
New-AzResourceGroup -Name "Storage-RG" -Location "eastus2"
New-AzResourceGroup -Name "VirtualMachine-RG" -Location "eastus2"
New-AzResourceGroup -Name "VirtualNetwork-RG" -Location "eastus2" 
```

![](https://images.bradleyschacht.com/wp-content/uploads/2019/11/building-an-azure-sandbox-part-1-the-basics-002.png)

## What location should I use?

Location. Location. Location. It's said of real estate all the time and the Azure cloud is no different. Every single resource that is created will ask you for a location which ties back to an Azure region. In general, you'll want to choose the closest region and use the same region for all your resources. The challenge, however, is that not all services are available in all regions. Certain VM sizes are only in certain places. Services light up in some regions ahead of others depending on hardware availability. There will be times when everything can't sit in the same region so you'll want to choose another one that's close by as a secondary or one across the country that will be used for DR or bringing the solution closer to a geographically distributed usergroup.

How do you know what location options there are? How do you know what the right name is to use for the location East US 2, or West Europe? As with all things...run a PowerShell command.

```powershell
Get-AzLocation
```

This command will provide a list of regions and the services available in each region. The location attribute is the value that should be placed into other cmdlets when prompted for the "location" parameter.

## Wrapping Up

At this point our shell is ready. Our code editor is configured. We've installed the necessary module to interact with Azure. We've connected to our subscription and created resource groups to hold everything that will be built in the coming posts.

I would encourage you to have the Azure Portal open at the same time you are running the PowerShell commands. Run a command, try to pull back the changes in PowerShell, and also go check the portal to see what has been created. Does it match up with what you expected to see? If not, do some research and try to figure out why and if there is anything you can do to control the created resources. Maybe that's a disk automatically created with a name you don't like, a virtual cluster, a resource group, etc. This is the time to learn it so when you're creating scripts to deploy to production you know exactly what the code is going to do.
