---
title: "MDX Functions: TopCount"
date: "2012-03-12"
categories: 
  - "business-intelligence"
---

The MultiDimensional eXpressions, or MDX, language is used to query the data in a multidimensional in an OLAP database.  In the case of examples here, SQL Server Analysis Services. This example uses the Contoso demo cube available for download here: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=18279 **Function Name:** TopCount **Use Case:** Brings back the highest X number of values from a set **MSDN Link:** [http://msdn.microsoft.com/en-us/library/ms144792.aspx](http://msdn.microsoft.com/en-us/library/ms144792.aspx) **Notes:** Always breaks a hierarchy **Syntax:** TOPCOUNT(<<MDX Set>>, Number of Tuples to return, <<Measure>>) **Sample Query:** `SELECT [Measures].[Online Sales Amount] ON COLUMNS, TOPCOUNT( [Product].[Product Category].CHILDREN, 2, [Measures].[Online Sales Amount] ) ON ROWS FROM [Contoso]` **Sample Results:** ![](https://images.bradleyschacht.com/wp-content/uploads/2012/03/TopCountResults.png)
