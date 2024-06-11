---
title: "Hide the Ribbon in Power View"
date: "2013-06-07"
categories: 
  - "business-intelligence"
---

There may come a day when you want to embed a Power View report into a PerformancePoint dashboard or a Web Part page in SharePoint. The first thing you will notice when entering the URL is the ribbon shows up in the web page viewer. The ribbon will be displayed both in PerformancePoint and in the page viewer web part. More often than not the desired result would be to not show the ribbon to prevent changes and improve the overall user experience. To hide the ribbon simply add **&ViewMode=Presentation&PreviewBar=False** to the end of the URL. Check out the screenshots from dashboard designer to see the difference below. With the ribbon visible: ![](https://images.bradleyschacht.com/wp-content/uploads/2013/06/PowerViewHideRibbon1.png) With the ribbon hidden, the added text on the end of the URL is underlined in red: ![](https://images.bradleyschacht.com/wp-content/uploads/2013/06/PowerViewHideRibbon2.png)
