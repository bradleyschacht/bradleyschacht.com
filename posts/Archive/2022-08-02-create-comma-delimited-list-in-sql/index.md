---
title: "Create Comma Delimited List in SQL"
date: "2022-08-02"
categories: 
  - "sql-server"
  - "syndication"
---

Previously, I [wrote a blog](https://bradleyschacht.com/comma-delimited-list-with-coalesce/) about how to create a comma separated list in T-SQL. 12 years later...one moment I have to go check and see if I'm really so old that I can say I wrote a blog post 12 years ago...

Ok, I'm back. It is confirmed. I am in fact officially old. I really did write that blog 12 years ago on June 23, 2010. Wow. Anyway...

12 years later that post has received dozens of views from me trying to remember how I did that. Well, I'm here to tell you there is a better way! I didn't discover this because I wanted to improve my code or because I was scanning the release notes for SQL Server to see what new T-SQL functionality has been released in the last few versions. No, I had to find a better way because my previous method which used a variable and the COALESCE function does not work on Azure Synapse Analytics.

The new method: [STRING\_AGG()](https://docs.microsoft.com/en-us/sql/t-sql/functions/string-agg-transact-sql?view=sql-server-ver16)

Let's get started with some sample data:

```
CREATE TABLE dbo.State
    (
        StateID [int],
        StateName [varchar](50)
    )

INSERT INTO dbo.State
SELECT 1, 'Florida'
UNION ALL
SELECT 2, 'Tennessee'
UNION ALL
SELECT 3, 'Georgia'
UNION ALL
SELECT 4, NULL
UNION ALL
SELECT 5, 'Texas'

SELECT * FROM dbo.State
```

![](https://images.bradleyschacht.com/wp-content/uploads/2022/08/create-comma-delimited-list-in-sql-001.png)

Now, a simple STRING\_AGG(expression, separator) and we are good to go!

![](https://images.bradleyschacht.com/wp-content/uploads/2022/08/create-comma-delimited-list-in-sql-002.png)

Quick and simple!

A couple quick notes in closing.

1. Notice that NULL values are ignored and the separator is not added. If you want to include NULL values you'll need to wrap the column name in this example with ISNULL so it would look something like SELECT STRING\_AGG(ISNULL(StateName, 'No Name Provided'), ',') FROM dbo.State
2. There is an optional order by operator that can be used to order the list.
3. The data type is determined by the expression. That means if the column is a string it will retain the string properties. MAX fields will result in a MAX data type. Non-max fields will result in the largest possible non-MAX value (VARCHAR(8000) or NVARCHAR(4000)). All other data types result in an NVARCHAR(4000) result.
4. You aren't limited to a literal string like a comma or a pipe for the separator. That's just my most common request. CHAR(13), CHAR(9), and just about any other expression are all valid as well.
