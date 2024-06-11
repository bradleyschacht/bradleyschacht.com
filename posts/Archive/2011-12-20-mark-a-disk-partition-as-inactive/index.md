---
title: "Mark a Disk Partition as Inactive"
date: "2011-12-20"
categories: 
  - "windows"
---

If you are like me then you like to reinstall Windows on your laptop every few months to keep things fresh and running smoothly.  That or your even more like me and you download some beta version of software and install it on your laptop rather than a VM and screw something up to an unrepairable state.  Either way Windows is being reinstalled.  I have a laptop without an optical drive in it.  The vast majority of the time I don't mind as I pretty much use ISO files exclusively.  This is a problem though when reinstalling the OS. I will usually extract all the Windows install files onto the root directory of my external hard drive, make it bootable (Mark the partition as active) and install from there.  The problem with that is you can't just go back into the disk management console and right click and unmark a partition as active. This causes it to go through the whole auto run thing every time you plug it in and if you boot while the drive is connected it gives you a fit because it wants to install the OS or can't find bootable files. Blah, blah, blah. So let's just mark it as an inactive partition and get it over with. You will know a disk has an active partition by going into the Disk Management Console.  You can see in the screenshot below, my external hard drive called My Passport (the F: drive) is marked as Active.

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/DiskManagementConsole.png)

To mark a partition as inactive follow these simple steps:

1. Open the Command Prompt
2. Type **DISKPART** (A new window will open)
3. Type **LIST DISK** at the prompt and a list of disks will be displayed numbered starting at 0
4. Type **SELECT Disk _#_** and replace the # with the appropriate disk from the list
5. Type **LIST PARTITION** and a list of partitions for the selected disk will be displayed
6. Type **SELECT Partition _#_** and replace the# with the appropriate partition form the list
7. Type **INACTIVE**
8. Either close the two windows or Type **EXIT** in both to close them.

The end result will look something like the screenshot below.

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/diskpart.png)
