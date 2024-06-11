---
title: "SharePoint SSRS “The permissions granted to user 'DomainUser' are insufficient for performing this operation”"
date: "2013-08-27"
categories: 
  - "sharepoint"
---

Not too long ago I ran into a crazy situation where just about everything that could have gone wrong with my SharePoint environment after a restore did go wrong. I was planning on blogging a bunch of it and thanks to time constraints I had to just get it backup and running and couldn't take down notes about most of it. After getting everything back up and running I thought I was good to go until two days later when an interesting issue came up. Running an SSRS report (SSRS 2012 using the SSRS Service Application) the first time would work fine, but if I changed the parameters or hit the View Report button a second time the following error would come up. ![](https://images.bradleyschacht.com/wp-content/uploads/2013/08/SharePoint-Error.png) \[error\]The permissions granted to user 'DomainUser' are insufficient for performing this operation. Microsoft.ReportingServices.Diagnostics.Utilities.AccessDeniedException: The permissions granted to user 'DomainUser' are insufficient for performing this operation.\[/error\] My execution account was set up and consequently not the account listed in the error message. The more intriguing part was that the account noted in the error message was was the service account running SSRS so it had all necessary rights on the reporting services databases. Just for the heck of it and to speed up my troubleshooting I made the account a sysadmin (yes, I know...I know...it was in DEV, calm down please) and the error persisted. According to section 8.3 of this MSDN page ([http://msdn.microsoft.com/en-us/library/ee384252(SQL.100).aspx](http://msdn.microsoft.com/en-us/library/ee384252(SQL.100).aspx)) there are two possible reasons for the given error:

- This is caused when the application pool account that runs the SharePoint Web application you are on in IIS and the application pool account that SharePoint believes is running the site differ. This happens if the application pool account is changed directly in IIS instead of through SharePoint (using the approved method).
- This error can be caused when Kerberos authentication has been set up correctly and delegation in a double hop scenario will succeed, but Reporting Services integration has been set up for Trusted Account rather than Windows Authentication. This causes SharePoint to continue to impersonate the logged-on user when contacting Reporting Services; when Reporting Services compares the application pool account to the user account making the request, they are not equal.

Awesome. Kerberos isn't in play so that narrows down my problem, right? If you've read many of my blog posts you will know the answer: WRONG! Of course my application pool account was the same in IIS and in SharePoint. The password for the account had not been changed and other applications using the same account were functioning just fine. As it turns out the first option had some merit. Since the two accounts were the same here is the final order of operations that fixed the problem for me:

1. Change the service account for the application pool in SharePoint to a **DIFFERENT** account
2. Change the service account for the application pool back to the desired account
3. Do an IIS reset on all SSRS application servers
4. Happy reporting

I didn't even need to touch the IIS interface to modify the account.
