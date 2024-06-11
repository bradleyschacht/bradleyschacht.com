---
title: "Open File - Security Warning in SSIS"
date: "2010-04-06"
categories: 
  - "business-intelligence"
---

When running packages that contain executable and batch files you may sometimes come across an issue where you need to confirm that you want to open the file. This can cause an issue if you run the package unattended. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/OpenFile1.png) Obviously, this is a security precaution that is built into the many of the newer versions of windows.You have to authenticate sessions, run as administrator, and confirm that you want to open and run a file that could possibly pose a security risk. We know that we want this file to run so we can tell Windows that we do not want to be warned about this any longer. Many times simply adding your local host to the trusted sites in Internet Explorer will solve this issue. If it is a file being run from another network machine, you can add that machine’s IP address as well. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/OpenFile2.png)

Please note that doing this will cause you to not be warned before you run .bat, .exe, etc. files, so this can pose a security risk if you are not careful. This works more often than not. However, there was an occasion when the server I was working on was locked down a little more and this was not quite enough to fix the problem. If you are still prompted to allow the program to run you can edit the group policy to not prompt you before running certain file types. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/OpenFile3.png) To edit the group policy go to **Start** > **Run** > Type in **gpedit.msc** to open the group policy editor. From there expand **User Configuration** \> **Administrative Templates** > **Windows Components** \> **Attachment Manager**. Next set **–Inclusion list for low file types-** to **Enabled** and enter the file types you don’t want to be warned about in the box (for instance, .bat or .exe). ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/OpenFile4.png)

Next time you run your packages you won’t be asked if you want to run the file, it will do it automatically!
