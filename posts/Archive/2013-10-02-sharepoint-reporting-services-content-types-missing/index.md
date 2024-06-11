---
title: "SharePoint Reporting Services Content Types Missing"
date: "2013-10-02"
categories: 
  - "sharepoint"
---

Being able to manage the content types in your SharePoint libraries is key to creating a useful portal. Among the necessary content types are those associated with SQL Server Reporting Services (SSRS). If you create a new document library and want to add the ability to create data sources, reports or even just launch report builder you will need to add new the reporting services content types to the library. This is a pretty simple operation. Enable the ability to manage content types in the library advanced settings to start with. Then in the content type manager find the necessary entries and add them to the "Content types to add" section and click OK. That proves to be more difficult when the SSRS content types are missing from the management screen as shown below. As you can see they don't appear in the left pane and they are also missing from the groups drop down menu. ![](https://images.bradleyschacht.com/wp-content/uploads/2013/10/Missing-1.png) ![](https://images.bradleyschacht.com/wp-content/uploads/2013/10/Missing-2.png) Assuming everything is configured correctly for SSRS in Central Administration you should check and make sure the reporting services integration is enabled in the site collection settings.

1. Navigate to **Site Settings**
2. Click the link for **Site Collection Features** in the **Site Collection Administration** section
3. Enable the **Report Server Integration Feature**

![](https://images.bradleyschacht.com/wp-content/uploads/2013/10/ReportServerIntegrationFeature.png) You should now see the reporting services content types and will be able to add them to your library. ![](https://images.bradleyschacht.com/wp-content/uploads/2013/10/ReportingServicesContentTypes.png)
