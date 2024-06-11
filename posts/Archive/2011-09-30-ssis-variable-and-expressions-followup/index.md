---
title: "SSIS Variable and Expressions Followup"
date: "2011-09-30"
categories: 
  - "business-intelligence"
---

Over the last week I have done several sessions either for the Pragmatic Works Free Training online or at SQL Saturday.  It has been a lot of fun, so thank you to everyone who has come out and hopefully learned something from one of the sessions I have done.  So, I mentioned a couple things I was going to do, one of which is post a quick recap and sample package from the Expressions and Variables session.  So here that is.  The other will come next week when I post some same situations and data sets that need to be cleaned up so you can try out using expressions to clean them up.  So the wrap up/followup/summary...

![](https://images.bradleyschacht.com/wp-content/uploads/2011/09/SSISExpression.png)

- Expressions and Variables are NOT independent, they can and often are used together
- If there is a user editable property for a task, then it is highly likely that you can put an expression on it
- Configurations are evaluated at package run time, a KEY DIFFERENT for expressions is that they are evaluated as they are come across in the package.  Consequently they can be evaluated many times if they are inside of a for each loop for instance
- Variables can be evaluated as an expressions, just be sure to set the property to use the expression to TRUE, again not sure why they even make you do that
- I'm currently listening to the new Blink-182 album, Neighborhoods, that came out this week and I saw them in Tampa Saturday after presenting at SQL Saturday.  IT WAS AWESOME!! That has nothing to do with this, just checking to see if you are skimming or reading everything in this blog
- Just a couple of places you can use expressions: variables, precedence constraints, connection managers, tasks, data flow transforms like the conditional split and derived column
- Most common use for expressions in the data flow is probably for data cleansing, but then again that is also a case by case sort of thing
- If you want to do things like append the date to the end of a file it can be done, but remember to cast the date parts as a string value and save the expression someplace since it will likely be used again in the future and it is pretty messy

I did mention that I would upload a sample package with some things we did in the session as well as the more useful piece of information, the contents of the notepad document I was copying and pasting from. It has some good expressions in there. So here are the expressions I had saved.  Feel free to use them as you come across a need for them or just save them for later.

- **Current Date in the format of YYYY-MM-DD**
    - (DT\_WSTR,4)YEAR( GETDATE() ) + "-" + RIGHT("0" + (DT\_WSTR,2)MONTH( GETDATE() ), 2) + "-" + RIGHT("0" + (DT\_WSTR,2)DAY(GETDATE()), 2)
- **Separate the file name from the full path**
    - RIGHT(@FilePathVariable, FINDSTRING( REVERSE(@FilePathVariable), "", 1 ) - 1 )
- **File Path with current date**
    - "C:SampleFile" + (DT\_WSTR,4)YEAR(DATEADD("dd", -1, GETDATE())) + "-" + RIGHT("0" + (DT\_WSTR,2)MONTH(DATEADD("dd", -1, GETDATE())), 2) + "-" + RIGHT("0" + (DT\_WSTR,2)DAY(DATEADD("dd", -1, GETDATE())), 2) + ".txt"
- **Convert Filing Date (Column in a sample data set in format MMDDYYYY) to an actual date**
    - (DT\_DBDATE)(SUBSTRING(FillingDate,1,2) + "/" + SUBSTRING(FillingDate,3,2) + "/" + SUBSTRING(FillingDate,5,4))
- **Pull back just the year from a date column**
    - YEAR(Columnname)
- **Replace the # In Address Line 2 with an empty string**
    - REPLACE(AddressLine2,"#","")
- **Replace the Word ROAD With RD**
    - REPLACE(AddressLine1,"ROAD","RD")
- **Replace blank country with NULL**
    - TRIM(Country) == "" ? NULL(DT\_WSTR,2) : Country
- **Replace blank country with Unknown**
    - TRIM(Country) == "" ? "Unknown" : Country
- **Conditional Split Examples**
    - **Missing Zip** TRIM(ZipCode) == ""
    - **Missing State** TRIM(State) == ""
    - **Missing Either** TRIM(State) == "" || TRIM(ZipCode) == ""

As always, be sure to check your results so that you don't do a replace on the string RD to ROAD and end up changing the address 123 Boardwalk Rd to 123 BoaROADwalk Road.  That would be bad. Here is a link to the file that I created in the two sessions from SQL Saturday. It is in a zip file and to properly illustrate it you will need the Contoso Database.  it is a free download from codeplex.  There is also a flat file that goes along with the expressions data flow that is included in the zip file.

Here is the zip file.  Please let me know if you have any issues getting it downloaded: [SQLSaturdayOrlando.zip](https://bradleyschacht.com/ssis-variable-and-expressions-followup/sqlsaturdayorlando/)
