---
title: "PowerPivot Data Connection Error"
date: "2011-12-15"
categories: 
  - "sharepoint"
---

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/datarefresherrorpowerpivot.png)

PowerPivot report returns the following error from Excel Services: **The data connection uses Windows Authentication and user credentials could not be delegated. The following connection failed to refresh: PowerPivot Data** When viewing a PowerPivot workbook in SharePoint 2010 the report is visualized using Excel Services since ultimately the report is inside an Excel workbook.  The root cause of this error is that Excel Services is unable to get a valid Windows account from whoever is running the report. After some research I found the following on MSDN.  I really wish I could take credit for finding all of these circumstances, but let’s be honest; I don’t have that much time on my hands. You will see this error when one of the following occurs:

- I have found that this is the most common reason: Claims to Windows Token Service is not running
- No domain controller is available to validate the Windows account
- The computers are not members of the same domain
- Excel Services account must have Active Directory permissions to query the object

Since the most common issue (again in my experience here) is the Claims Token Service is not running I’ll show you how to fix that and hopefully you will be back to PowerPivot-ing in no time. To enable the Claims to Windows Token Service in SharePoint 2010:

1. Open **SharePoint 2010 Central Administration**
2. Navigate to the **System Settings** section
3. Click **Manage services on server**
4. Click **Start** next to **Claims to Windows Token Service**

When the Claims to Windows Token Service is stopped it will look like the screenshot below. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/claimstowindowstokenservicecentraladministration.png) References: [http://msdn.microsoft.com/en-us/library/ff487975.aspx](http://msdn.microsoft.com/en-us/library/ff487975.aspx)
