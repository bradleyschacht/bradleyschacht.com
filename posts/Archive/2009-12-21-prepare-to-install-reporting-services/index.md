---
title: "Prepare To Install Reporting Services"
date: "2009-12-21"
categories: 
  - "business-intelligence"
---

The first step in setting up a report server is getting Reporting Services installed.  There are a few differences between the process for 2005 and 2008.  On the installation side, if you do not have the IIS setting configured before running the SQL install process you will not even be given the option to install reporting services in 2005.  You still have the option even without setting up IIS in 2008.  You will need a version of SQL Server that has reporting services, which includes the Standard, Enterprise, Workgroup and Evaluation editions.  The following changes are necessary to install reporting services 2005. Before running the SQL Server Setup Wizard make the following changes: In Windows Vista and Windows 7 the changes can be made by going to **_Control Panel > Programs and Features > Turn Windows Features on or off_** ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/SSRSInstall1.png) From the box that opens check each of the following bold items:

- IIS
    - Web Management
        - IIS6 Management Compatibility
            - **IIS WMI Compatibility**
            - **IIS Metabase and IIS 6 configuration compatibility**
- World Wide Web Services
    - Application Development Features
        - **.NET Extensibility**
        - **ASP .NET**
        - **ISAPI Extensions**
        - **ISAPI Filters**
    - Common HTTP Features
        - **Default Document**
        - **Directory Browsing**
        - **HTTP Errors**
        - **HTTP Redirection**
        - **Static Content**
    - Security
        - **Request Filtering**
        - **Windows Authentication** 

 **_![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/SSRSInstall2.png)_**In Windows Server open the Server Manager, then on the left side expand **_Roles > Web Server (IIS)_** then click **_Add Role Services_** in the right pane**_._** **_![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/SSRSInstall3.png)_** You will see a series of check boxes come up, of which the following bold items need to be checked:

- Web Server
    - Common HTTP Features
        - **Static Content**
        - **Default Document**
        - **Directory Browsing**
        - **HTTP Errors**
        - **HTTP Redirection**
- Application Development
    - **ASP .NET**
    - **.NET Extensibiliity**
    - **ISAPI Extensions**
    - **ISAPI Filters**
- Health and Diagnostics
    - **HTTP Logging**
    - **Request Monitor**
- Security
    - **Windows Authentication**
    - **Request Filtering**
    - Management Tools
        - **IIS Management Console**
        - IIS 6 Management Compatibility
            - **IIS 6 Metabase Compatibility**
            - **IIS 6 WMI Compatibility**
            - **IIS 6 Scripting Tools**
            - **IIS 6 Management Console**

![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/SSRSInstall4.png) From this point on any system, you are ready to run the SQL Server Setup Wizard.  Getting these setting is vital to having a working report server.  A missing setting could cause errors down the road, anything from permissions issues, to an error message telling you the report server is not there, when you know for sure it is…because you installed it.  During the install process you can chose the option to “Install the default configuration” or “Install but do not configure the server” which is completely up to you.  Remember to install any service packs and updates after you complete the initial installation.  In the next article in this series we will take a closer look at report server configuration including securing your report server with an SSL certificate.
