---
title: "Comma Delimited List with COALESCE"
date: "2010-06-23"
categories: 
  - "sql-server"
---

There may be a day when you need to create your own comma delimited list from data contained in a SQL table.  Sure you could build a cursor and go through each row adding a comma after each record.  The good news is that there is an alternative.  The better news is that this method is easier, faster and takes MUCH less code.  COALESCE in SQL has many uses and building a list is one of them.  To do this in our context use the following format: COALESCE(@Variable + _Delimiter_, _WhatToDoWhenThereAreNoMoreResults_)  The following code will bring back a list of all the states in my State table adding a comma after each one.  We want a comma delimiter and when there are no more results we want to end with nothing so we don't have a trailing comma.  Therefore we will use COALESCE(@State + ', ', '').  Now we just need to build a SQL statement around that to add a state name to the end of the string each time a new one is found.  The following code will accomplish that task.  @State will start out empty so no comma will be added to it but the first state name will populate the variable.  Each subsequent state name will have a comma added before it thanks for the COALESCE function being before the state name that is selected next. DECLARE @State varchar(800) SELECT @State = COALESCE(@State + ', ', '') + StateName FROM State Select @State Now you can build your own delimited lists!
