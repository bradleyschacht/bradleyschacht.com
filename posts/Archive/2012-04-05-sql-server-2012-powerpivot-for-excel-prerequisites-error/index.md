---
title: "SQL Server 2012 PowerPivot for Excel Prerequisites Error"
date: "2012-04-05"
categories: 
  - "business-intelligence"
---

PowerPivot was a great new tool when it released several years ago.  Now the newest version is out along side SQL Server 2012.You can still install the 2008 R2 version of PowerPivot or you can try out the new 2012 RTM version.  There are some nifty improvements that Devin Knight has chronicled ([Blog](http://devinknightsql.wordpress.com/2012/03/07/powerpivot-2012-rtm-release/) | [Twitter](http://twitter.com/#!/knight_devin)). There are several requirements that are not part of the 2008 R2 requirements.  The install instructions are the following with the first step being the new requirements:

- Install [.NET Framework 4.0](http://go.microsoft.com/fwlink/?LinkId=232658) and [Visual Studio 2010 Tools for Office Runtime.](http://go.microsoft.com/fwlink/?LinkId=232657)
- If you are running Windows Vista or Windows Server 2008, install the platform update.
- Install Microsoft® Office 2010.
- Install PowerPivot for Microsoft Excel

There are plenty of blogs out there discussing the error message that says you need to install the proper version (32 bit or 64 bit) because you downloaded the incorrect one for the version of office installed. However, I installed both the prerequisites (.Net 4 and Visual Studio Tools) and still had the following error: "**Microsoft SQL Server 2012 PowerPivot for Excel Setup** **Setup is missing prerequisites:** **This add-in requires the Microsoft Visual Studio 2012 Tools for Office Runtime and the .NET Framework 4.0. For more information, go to <some website URL>**" ![](https://images.bradleyschacht.com/wp-content/uploads/2012/04/PowerPivot-2012-Install.png) Come to find out if you have both of these already installed and then try to install PowerPivot and get this error message the fix is pretty simple and one that is not listed on the Microsoft page about the install. You have to install the components in a certain order:

1. .Net Framework 4.0
2. Microsoft Visual Studio 2010 Tools of Office Runtime
3. PowerPivot 2012 for Excel

The problem I had was installing the Visual Studio 2010 Tools, then .Net Framework 4.0 then PowerPivot. Luckily you don't need to roll everything back. Simply uninstall the Visual Studio 2010 Tools and reinstall them, this way they are put on the system AFTER .Net 4. I tried running a repair on the Visual Studio Tools about 4 times at different point after the .Net 4.0 install was finished with no luck. I did a simple uninstall and immediate reinstall and everything worked PERFECT! So when setting up PowerPivot 2012 be sure to install in the order listed above.  If you do switch number 1 and 2 just uninstall the Visual Studio Tools and run the install again. Worked like a charm.
