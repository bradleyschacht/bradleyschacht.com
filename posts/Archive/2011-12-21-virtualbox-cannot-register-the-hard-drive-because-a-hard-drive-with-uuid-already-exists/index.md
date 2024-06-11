---
title: "VirtualBox - Cannot Register the Hard Drive Because a Hard Drive with UUID Already Exists"
date: "2011-12-21"
categories: 
  - "windows"
---

Virtualization is awesome! It really helps when you are a developer.  On my laptop I don't even have SQL Server installed on the OS directly.  In fact the only thing I have installed on my host is Office, Skype and Chrome.  Everything else gets run on a VM. SSIS package creation using BIDS: VM. SQL Server Management Studio: VM. SharePoint sandbox: VM VPN: VM. One of the advantages of doing this is you don't have to worry about screwing something up.  I have a snapshot on the VM just before I installed SQL Server.  So if something happens and my install gets corrupted I will just roll it back and reinstall SQL Server rather than the entire OS plus SQL Server. Currently to do all this I am using Oracle's VirtualBox software.  It works great for those of us who have Windows 7 and allows you to run both x86 and x64 virtual machines.  I recently tried to make a copy of one of my VHDs (Virtual Hard Drive) and create a new VM.  Everything was wonderful until I got this error: ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/VirtualBoxDuplicateUUID.png) **Failed to open the hard disk <FilePath>.** **Cannot register the hard disk <FilePath><GUID> becuase a hard disk <DifferentFilePath> with UUID <GUID> already exists.** I looked all over the place to find what was causing it and finally found a fix. Of course it requires the command prompt, so open that junk up and let's get started!

**\*\*Note that I am on VirtualBox version 4.1.6 and from what I've read this command used to be something different a few versions ago. I think instead of setdhuuid it was setvdiuuid, but don't hold me to that.\*\***

1. Open the Command Prompt
2. Change the directory to where VirtualBox is installed (Default: C:Program FilesOracleVirtualBox)
3. Type the following **VBOXMANAGE.EXE internalcommands sethduuid <PathOfNewVHD>**

Note that **internalcommands** and **sethduuid** need to be lowercase.  I spent more than a couple minutes trying to figure out why things weren't working.  :) Below is the output of me changing the ID of the VHD located at C:UsersbschachtVirtualBox VMsWindows XP (VPN) Copy.vhd ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/VirtualBoxCMDOutput.png)
