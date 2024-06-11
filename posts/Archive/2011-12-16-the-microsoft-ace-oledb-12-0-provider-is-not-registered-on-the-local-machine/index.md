---
title: "The Microsoft.Ace.OLEDB.12.0 Provider Is Not Registered on the Local Machine"
date: "2011-12-16"
categories: 
  - "business-intelligence"
---

If you have used SSIS to move data from something like Access or more commonly Excel, it is a good possibility you have seen this error at some point:

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/aceoledbprovidermissing.png)

**Error: The 'Microsoft.Ace.OLEDB.12.0****' provider is not registered on the local machine.**

The reason this error shows up is because the proper components are not present that are required to move data from Office files (I'm looking at you Access and Excel). If you do development on a laptop or desktop then there is a very good chance that Office is installed on the machine and this error will likely never present itself.  However, when the package is run or developed on a server, where Office is generally not installed and shouldn't be, the proper components for Access and Excel interaction do not exist. The solution is simply installing the components required from the Microsoft Access Database Engine available here ([http://www.microsoft.com/download/en/details.aspx?id=13255](http://www.microsoft.com/download/en/details.aspx?id=13255)) at the time of this post. There is a 32 and 64 bit download so be sure to match that to the machine that has the error. Despite the name of the download it actually works for Access AND Excel. A note if you still have issues after installing the components here: The provider portion of the connection string should read **Microsoft.ACE.OLEDB.12.0** and if connection to Excel the extended property of **Excel 14.0** may need to be added. **UPDATE:** A little more information on some scenarios that some people have run across. The machine in question was a 64-bit OS with 64-bit office 2010 installed. So the above components are irrelevant in that case as they are already installed with office. The error was still showing up. The fix is to download and install the 2007 version of the Access Database Engine available here [http://www.microsoft.com/en-us/download/details.aspx?id=23734](http://www.microsoft.com/en-us/). I haven't tested to see if just installing the 2007 components is the best option, but for now it seems that you should go ahead and grab that download and get that version of the engine installed.
