---
title: "SQL Maintenance Plan Doesn't Delete Backups"
date: "2009-12-08"
categories: 
  - "sql-server"
---

I recently created a maintenance plan for our server to backup all the databases on a daily basis.  In the cleanup step I set it to delete backups older than 7 day.  I let the plan run for a week and checked on it to make sure the old backups were being deleted.  This would be kind of a useless post if everything was working correctly, so as you guessed, they were not being deleted.  I check everything on the plan: Username, folder name, first level subfolders, permissions on the backup folder, sql server agent permissions.  You name it.  All to find out that my problem was in the maintenance plan all along.  The option for file extension was the issue.  I had entered .bak when I should have entered bak.  I was thinking hey, I set up a foreach loop to loop through text files I enter .txt so for backup files I should enter .bak: WRONG!  Once I changed this option from .bak to bak everything worked fine.  Now my maintenance plan backups and deletes everything older than a week! ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/MaintenancePlan.png)
