---
title: "Backup and Restore SharePoint 2010 Site Collection with PowerShell"
date: "2011-07-13"
categories: 
  - "powershell"
  - "sharepoint"
---

One of the most annoying processes with SharePoint is backing up a site collection.  There is a build in tool that you can use but it is kinda clunky and can take a while to perform the backup.  I used it once and was not impressed.  PowerShell provides a quick and easy way to do site collection backups.  My favorite part is that you can do the entire backup with one, uno, un, 1 (that's all I know for the number one so... yeah) line of code.  Just a little something to be careful of, you need to use the Windows SharePoint 2010 Management Shell, not the regular Windows PowerShell.  I spent about 15 minutes trying to figure out why it (Windows PowerShell) didn't think that Backup-SPSite was a valid command.  Here are the steps you should take.  Note that doing the backup will put your site into a read only mode.

![](https://images.bradleyschacht.com/wp-content/uploads/2015/08/how-to-use-the-unpivot-transform-in-ssis-2.png)

1. Click **Start**
2. Go to **All Programs**
3. Go to **Microsoft SharePoint 2010 Products**
4. Open **SharePoint 2010 Management Shell**

A PowerShell command prompt will appear and you need to format the following to fit the backup for your site. `Backup-SPSite -Identity SiteCollectionURLHere -Path BackupFilePathHere [-Force] [-NoSiteLock] [-UseSqlSnapshot] [-Verbose]` I recommend creating a folder where you can place these backups before starting the backup process so they aren't just chillin on the C: drive of your SharePoint server; just a thought.  Here is a little explanation of those additional parameters that are inside the braces \[ \]

- Force - Include this if you want to override a backup with the same name
- NoSiteLock - Will prevent the site from going to Read Only mode while the backup is being taken.  A small warning, if someone changes content on the site while the backup is being created and according to Microsoft "might lead to possible data corruption"
- UseSQLSnapshot - A database snapshot will be taken before the backup begins and the backup will be done off the snapshot. The advantage is that changes can be made to the site while the backup process is running without fear of corruption.  The snapshot will be deleted automatically when the backup is completed.  You don't need to specify the -NoSiteLock parameter when using this method

Here is a simple example of what the script may look like if you want to just do a backup: Backup-SPSite -Identity http://servername/sites/BISite -Path D:SharePointBackupsBISite7-13-2011.bak To do site restores the syntax is almost just as easy.  You will need to use the same SharePoint 2010 Management Shell as doing the backup. `Restore-SPSite -Identity SiteCollectionURLHere -Path BackupFilePathHere [-DatabaseServer DatabaseServerNameHere] [-DatabaseName ContentDatabaseNameHere] [-HostHeader HostHeaderHere] [-Force] [-GradualDelete] [-Verbose]`

- DatabaseServer - Specify the server for the content database
- DatabaseName - Specify the name of the content database
- HostHeader - URL of the Web application that will hold the host-named site collection
- Force - Overwrite the site collection if it exists
- GradualDelete - Recommended for site collections over 1 Gig in size, existing data is marked as deleted and gradually removed over time by a job rather than all at once to reduce the performance hit of deleting large amounts of data

Hopefully this will save you some time and a headaches as it did me!  As much as I don't like coding and I stay away from PowerShell, I will admit, it is a huge time saver and super easy in the SharePoint site collection backup/restore world.
