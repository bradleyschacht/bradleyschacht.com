---
title: "Date Dimension Script with Fiscal Year"
date: "2012-03-06"
categories: 
  - "business-intelligence"
  - "sql-server"
---

One dimension you can be positive will always make it's way into your data warehouse is the Date dimension.  Over the past couple of years I have taken a look at a lot of Date dimension scripts and found quite a few good ones.  Well recently I needed to add fiscal date to the dimension and the script I had didn't let me do that properly.  Rarely does the fiscal year start and end on the same dates every year.  Most companies adopt some kind of 4-4-5 or 4-5-4 model for their year. The script below will allow you to, if you choose, add fiscal date to the dimension.  I just did some modifications on this script (since I combined a bunch that I have found and rewrote/added things I needed over time) so please let me know if you find any issues with it and I will happily update it as soon as possible!  If you see some of your code in here please leave a comment as I can't remember all the places where I have pulled from, but there are many great resources that went into this script collaboration, I can't take credit for it all. Bookmark this page and/or save a copy of the script, chances are you will need it again sometime in the near future. I do have to add the portion back in where I add the indexes on, so I apologize for that missing right now. This file contains:

1. DimDate.sql - The new combined SQL script with both calendar and fiscal.  This also includes several fields that are NOT in the other files
2. Date Dimension.sql - A version of the Date Dimension I have used for a while, but does not have fiscal date in it and does not have the added fields from the first script.
3. Date Dimension Add Fiscal Date.sql - Adds fiscal dates to the second script.  This logic has been folded into  script number 1 with the same columns that are included here.

Download the Zip file here: [DimDate](https://images.bradleyschacht.com/wp-content/uploads/2012/03/DimDate.zip)
