---
title: "Check Version of SharePoint 2010 Currently Installed"
date: "2012-01-11"
categories: 
  - "sharepoint"
---

There are several ways to check the version of SharePoint 2010 currently installed in your environment. That about sums up what we are talking about here.  Check it out below. **Method 1:** Through SharePoint 2010 Central Administration:

1. Open **SharePoint 2010 Central Administration**
2. Click on the link for **System Settings**
3. Click the link for **Manage Servers in This Farm**
4. Look at the **Configuration Database Version** field

![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/SPCentralAdministrationCheckVersion2.png)

**Method 2:** A detailed list through SharePoint 2010 Central Administration:

1. Open **SharePoint 2010 Central Administration**
2. Click on the link for **Upgrade and Migration**
3. Click the link for **Check Product and Patch Installation Status**
4. Look at the **Version** column for the release your server is at

**![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/SPCentralAdministrationCheckVersion.png)**

- There is a view button in the top left that will change the list from the farm to individual servers in the farm
- At the top of the page a link labeled **Click here for the latest information on available updates for SharePoint 2010 Products** will take you to a list of all cumulative updates for SharePoint.
- Any Hotfix will show under the product it applied to (my screenshot shows an RTM server with no hotfix installed)

**Method 3:** PowerShell

1. Open **SharePoint 2010 Management Shell** from the Start menu (right-click and select **Run as Administrator**)
2. Type  **(get-spfarm).buildversion**

**![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/SPPowerShellCheckVersion.png)**
