---
title: "Building an Azure Sandbox"
date: "2019-11-06"
categories: 
  - "azure"
  - "syndication"
---

It's that time of the year again. No, not fall, although I do love the weather change that's around the corner. It's time for me to rebuild my Azure environment!

Each year I try to wipe out as much of my Azure environment and rebuild it as I possibly can. I like to keep up on product updates that I may have missed, try out new services that I haven't gotten around to just yet, learn new PowerShell commands, and if I'm being honest, organize my environment because I'm a little crazy about things being tidy. Most of the time I do this in July as that's the "slow" time of the year for Microsoft. I'm a little late because there is no real slow time but with Ignite being this week as I get started I thought it would be good timing. Most of the time I remove most of my environment and rebuild. I'll update servers that I want to keep around, I'll clean up my storage account, etc. This year I took a slightly different approach...

![](https://images.bradleyschacht.com/wp-content/uploads/2019/11/building-an-azure-sandbox-001.png)

I just deleted everything. All of it.

Sometimes you just need to get a fresh start. With this fresh start I thought I would maybe try something new too. I am going to see about rebuilding my entire environment using PowerShell and I'm going to post the journey here. I don't have access to everything that my customers use (I'm not an admin on Microsoft's Azure AD, for instance) but I have enough to be able to post about some of the pitfalls that you may face. This will hopefully also serve as a guide for others to build out a sandbox environment for your own learning purposes.

You don't need to do everything the same way I'm going to do it. I may not use best practices everywhere because I either don't have access or I'm just taking shortcuts, so don't take what I'm doing here and apply it to your production environments at all. I may get part of the way down the road and decide I want to change something too.

Check back with me over the next few weeks as I build things out. Topics will include, but will not be limited to, but also may change, but also might shift around:

1. Networking
2. IaaS Domain
    1. Domain Controller
    2. Workstation
    3. SQL VM(s)
3. SQL
    1. Single Database
    2. Managed Instance
4. Storage Accounts
5. Various Data PaaS services for random demos

I have decided I'm going to work on getting more familiar with Visual Studio Code through this whole process as well. I use it for an editor but I don't use it for PowerShell at all, and I don't use it to connect to or run SQL. I'm very comfortable in the PowerShell ISE and SQL Server Management Studio but I want to use the newer tools more, so I'm forcing myself to learn them now! Also, with the announcement of Visual Studio Online this week, it's a great time to get started in VS Code or just spin up an environment online and get started!

Regardless of which route you decide to go, if you follow me on this Visual Studio adventure as opposed to the PowerShell/PowerShell ISE route, then you will want to install the PowerShell extension. There is also a function that annoys the mess out of me where if you run a line of code the cursor automatically goes down to the console rather than staying up on your script. So I always have to then click back up top. No thank you. So I added a line to my settings.json file to make sure that doesn't happen.

```bash
    "powershell.integratedConsole.focusConsoleOnExecute": false
```

I think that about covers it for today. Check back soon to see what kind of things I'm building and follow along in your environment!
