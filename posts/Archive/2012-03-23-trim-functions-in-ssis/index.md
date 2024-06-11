---
title: "Trim Functions in SSIS"
date: "2012-03-23"
categories: 
  - "business-intelligence"
---

In T-SQL there are many functions there to help do a variety of different things.  SSIS is no different.  In fact, a good number of the commonly used functions from T-SQL are in SSIS as well.  Here we will explore the different Trim functions found in SSIS. We will be using the test phrase "      Test      " to illustrate the three functions: LTRIM, RTRIM and TRIM. **LTRIM (Left Trim):** Just as in T-SQL, this removed empty spaces from the beginning (or left) of a string. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/03/LTRIM.png) Notice the highlighted result that the trailing spaces are still present but the leading spaces are gone. **RTRIM (Right Trim):** Just as in T-SQL, this removed empty spaces from the end (or right) of a string. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/03/RTRIM.png) Notice the highlighted result that the trailing spaces are gone but the leading spaces are still present. **TRIM:** In T-SQL to remove both leading and training spaces you have to nest the trim statements like this: SELECT LTRIM(RTRIM(ColumnName)) AS ColumnName FROM Table. Luckily in SSIS there is a TRIM function that will remove both leading and trailing space in one function called TRIM. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/03/TRIM.png) Notice the highlighted result that the trailing and leading spaces are gone.
