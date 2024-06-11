---
title: "Remove Local Administrators SSAS Admin Access"
date: "2013-03-05"
categories: 
  - "business-intelligence"
---

Recently while changing some production security around on a new server I needed to modify the SQL Server Analysis Services (SSAS) administrator group at the server level. This is a pretty easy process; log in, add or remove admins as necessary in the security section. However, by default any user who is a member of the local administrator group on the server will also be an administrator on the SSAS instance. Luckily there is an advanced property on the SSAS instance to change this and therefore remove the admin access. Just make sure there is someone set as an admin on the Security properties for SSAS before making this change otherwise get your installation media ready for a re-install! To remove admin access for users in the Local Administrators group:

1. Launch SQL Server Management Studio
2. Log onto the SSAS instance
3. Go to the server properties (Right-Click on the server name and select Properties)
4. Go to the General settings
5. Check the box to **Show Advanced (All) Properties**
6. Find **Security BuiltinAdminsAreServerAdmins** and change the value column to **false.** 

![](https://images.bradleyschacht.com/wp-content/uploads/2013/03/BuiltInAdminsAreServerAdmins.png)
