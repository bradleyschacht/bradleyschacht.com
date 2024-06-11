---
title: "Find Users Logged Into Remote Computer"
date: "2014-07-23"
categories: 
  - "windows"
---

Have you ever wanted to find out if someone is logged into a computer or maybe who is logged into a computer without having to remote in to check? Recently I was working on a project doing some SharePoint work using 3 accounts: Admin/Install, Farm and Service. The work I was doing required a lot of remote desktop switching between those three accounts, across three environments, plus the SQL Servers and SQL Server service account. Rather than opening up Remote Desktop and logging into each server to see what accounts were logged in I figured I would fire up the good ol' command line and check that way. There are tons of other ways to do this using PowerShell and I'm sure other command line methods, but this one is quick and easy. Just fire up the command prompt or PowerShell and run this: query session /server:"<Server Name Here>" query session /server:"KERBEROS-SP" ![](https://images.bradleyschacht.com/wp-content/uploads/2014/07/UsersLoggedInRemote_1.png) As we see in the screenshot there is one user logged into this computer, the Administrator account. If you want to check multiple computers just create a list in notepad and save it as a batch file to run. This clearly won't work for a large quantity of servers, but it worked great for the 6 servers I needed to check. Unfortunately if the user does not have the appropriate permissions on the server they will not get an error message. Instead the result will list no sessions. ![](https://images.bradleyschacht.com/wp-content/uploads/2014/07/UsersLoggedInRemote_2.png)