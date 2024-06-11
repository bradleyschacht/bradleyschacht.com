---
title: "Unsupported Features in Excel Services SharePoint 2010"
date: "2012-04-02"
categories: 
  - "sharepoint"
---

![](https://images.bradleyschacht.com/wp-content/uploads/2012/04/RefreshableRegion.png) Between SharePoint 2007 and SharePoint 2010 there were many new features that were added or enhanced.  There still remains a list of items that are either unsupported actions, such as workbook authoring, or unsupported features, such as cell comments. In Excel Services 2007 many unsupported features would cause the workbook to not load at all and have since had the behavior changed in 2010.  The following is a list of features that would prevent the workbook from loading in 2007.  They are still unsupported in 2010, but will render as static objects and still let you interact with the rest of the Excel workbook:

- Cell comments.
- Formula references to external books.
- Query tables (also known as external data ranges).
- Microsoft Visual Basic for Applications (VBA).
- Any OfficeArt technology. For example, Shapes, WordArt, SmartArt, organization chart, diagrams, signature lines, ink annotations, and so on.

One common error is "**The current selection is not within a refreshable region of the workbook.**" The main location you will run into this is when trying to refresh a table.  As noted above query tables are still not supported, but will render static in the workbook.  If you want data refresh on those values they need to be in a Pivot Table. A full list of unsupported features can be found on MSDN. Supported and Unsupported Features in Excel Services SharePoint 2010: [http://msdn.microsoft.com/en-us/library/ff595319.aspx](http://msdn.microsoft.com/en-us/library/ff595319.aspx) All unsupported features from SharePoint 2007 are still valid and behave the same way unless mentioned in the link above for Supported and Unsupported Features in SharePoint 2010. A link for the Unsupported Features in Excel Services SharePoint 2007 can be found here: [http://msdn.microsoft.com/en-us/library/ms496823.aspx](http://msdn.microsoft.com/en-us/library/ms496823.aspx)
