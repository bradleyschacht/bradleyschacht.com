---
title: "Hyper-V The trust relationship between this workstation and the primary domain failed"
date: "2015-10-27"
categories: 
  - "syndication"
  - "windows"
tags: 
  - "hyper-v"
---

Note this fix only works on Windows Server 2012/Windows 8 or later. A different method is required on Windows Server 2008R2 or earlier which involves the use of the NETDOM command. For several years now I have been using Hyper-V for building out a virtual development environment on my laptop. Since I work as a consultant I have to handle a lot of different software scenarios. Some customers use web based VPN, others use software, sometimes I have to install a specific version of SQL while other times I remote into a workstation. On top of my customer needs I frequently do presentations at SQL Saturdays and for Pragmatic Works. All of these things combine to mean I have to be ready for just about anything in relatively short amounts of time.

> "FIXED: Hyper-V trust relationship between the workstation and domain failed."

I learned early on that rebuilding my laptop every time I needed to change a configuration for a given project or presentation was going to take up a huge chunk of my time so I started virtualizing. The issue that I run into from time to time comes into play when I have to roll back my VM to a previous checkpoint (aka snapshot). The problem manifests in an error message when attempting to log into Windows after applying a checkpoint and reads: **The trust relationship between this workstation and the primary domain failed.** ![](https://images.bradleyschacht.com/wp-content/uploads/2015/10/hyper-v-the-trust-relationship-between-this-workstation-and-the-primary-domain-failed-1.png) The solution for this particular error message is fairly simple but does require you to know the credentials for a local user account that is an administrator.

* * *

Here is a little bit of background information that will come in handy over the next few steps as we resolve the issue:

- Domain Name: BSCORP
- VM Computer Name: BS-WS-01
- Domain Admin Account: BSCORP\\Administrator
- Local Admin Account: Bradley

* * *

First, log into the VM that is displaying the trust error message using a local account that is an administrator. In this case I'm going to log in using the account "Bradley". ![](https://images.bradleyschacht.com/wp-content/uploads/2015/10/hyper-v-the-trust-relationship-between-this-workstation-and-the-primary-domain-failed-2.png) Second, run PowerShell as an administrator and run the Reset-ComputerMachinePassword command. Be sure to replace the credentials shown below with your own domain admin account. Also note that when you run this command a popup will appear asking you to input the password for the account specified in the PowerShell command. **Reset-ComputerMachinePassword -Credential BSCORP\\Administrator** ![](https://images.bradleyschacht.com/wp-content/uploads/2015/10/hyper-v-the-trust-relationship-between-this-workstation-and-the-primary-domain-failed-3.png) There is an optional parameter on the Reset-ComputerMachinePassword cmdlet to specify a domain controller in the environment as well. If you do not specify this parameter a domain controller is chosen for you. Reset-ComputerMachinePassword -Credential <DomainName\\AccountName> -Server <DomainControllerMachineName> Finally, restart the VM and log in with the original credentials that you were using when the domain trust error message appeared.
