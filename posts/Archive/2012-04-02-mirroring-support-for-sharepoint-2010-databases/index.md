---
title: "Mirroring Support for SharePoint 2010 Databases"
date: "2012-04-02"
categories: 
  - "sharepoint"
---

Like all systems built on top of SQL Server SharePoint 2010 needs to have a plan for availability.  There are many methods for configuring high availability, one of which is database mirroring. Other options, such as clustering will be discussed separately. Database mirroring is a SQL Server technology used for providing database redundancy.  It is configured on a database level, not the server level. With this method any transaction on the the principal server are sent to the mirror server/database as soon as the transaction log buffer is written to disk on the principal server/database. The downtime in failover is generally measured in seconds, not minutes. SharePoint supports both mirroring and clustering for failover.  While both do a great job in the case of a failure the question often comes up as to what databases support each option. Many service applications create their own databases, such as the Search Service creating an administration, crawl and property database. The vast majority of the databases support mirroring with the exception of only two at the time of writing.  Keep in mind this is information taken from MSDN and applied to SQL Server 2008 R2 and SharePoint 2010. This information could change for SQL Server 2012 as the back end or with future versions of SharePoint. The only two databases that do _**not**_ support mirroring are:

- User Profile Service: Synchronization database
- Web Analytics Service: Staging database

Here is relevant article, the note about the lack of support for mirroring is in the notes under each service application: [http://technet.microsoft.com/en-us/library/cc748824.aspx](http://technet.microsoft.com/en-us/library/cc748824.aspx)
