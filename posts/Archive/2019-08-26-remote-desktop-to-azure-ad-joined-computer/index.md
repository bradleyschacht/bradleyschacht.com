---
title: "Remote Desktop to Azure AD Joined Computer"
date: "2019-08-26"
categories: 
  - "windows"
---

I'm frequently on the move and switch between devices. I could be working at home one day, at a hotel working, on site at a customer office with no internet, on a plane. I just never know. Part of my workflow is running some of my daily activities on an Azure VM. I can get to it from pretty much anywhere and it doesn't matter what device I do or don't have access to, I can always get to what I need and access the Microsoft network.

In order to access everything from our corporate network I have joined the computer using Azure Active Directory (Azure AD). Before I show you how to remote desktop to an Azure AD joined VM or computer, let me show the steps to join a computer to Azure AD. This requires the machine to be running Windows 10 version 1709 or later to connect to Azure AD but 1809 or later to remote desktop with Azure AD credentials. This can be a physical computer or a virtual machine.

**Join a Computer to Azure Active Directory**

First, launch the Windows Settings app and navigate to the **Accounts** section.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-001.png)

Using the left side navigation go to the **Access work or school** section and click **Connect**.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-002.png)

On the resulting screen click the link at the bottom of the page labeled **Join this device to Azure Active Directory.**

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-003.png)

Proceed through the wizard by entering your email address, authenticate with your company's preferred method, and verify the domain information.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-004.png)

Upon completion the work or school access screen will now show that you are connected to your organizations Azure AD along with the account used to connect.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-005.png)

**Remote Desktop to Azure AD Joined Computer**

Unfortunately, at this time it isn't quite as easy as "open up a new RDP connection, type in the computer, type my email, and connect". If it were, this post wouldn't be here. So let's look at the steps we need to go through to get connected.

First, open remote desktop as if you were going to connect to any other computer. Type in the computer name or IP address and expand the the **Show Options** section. Next, click the **Save As** button to save the RDP file locally. I'm going to place mine on my desktop. At this point you can close the Remote Desktop Connection dialog. It isn't needed anymore.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-006.png)

Next, open Notepad. Click File -> Open -> location your RDP file that was saved in the previous step. You'll need to change the document type dropdown from Text Documents (.txt) to All Files (\*).

Go to the very bottom of the list of parameters and add the following two lines:  
enablecredsspsupport:i:0  
authentication level:i:2

Save the changes to the .rdp file. Note that your file may have more or fewer lines in it than mine.

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-007.png)

Now you are ready to connect! Double click on the RDP file and fill in the dialog box.

For the user name field should be formatted as **.\\AzureAD\\email@company.com**  
(Technically it only needs to be AzureAD\\email@company.com but there are some strange caching things that happen when the VM autolocks and you go to sign back in. Adding the dot slash (.\\) at the beginning will save you some headache of having to add AzureAD\\ to the beginning of your user name each time you try to log in.)

![](https://images.bradleyschacht.com/wp-content/uploads/2019/08/remote-desktop-to-azure-ad-joined-computer-008.png)

There you have it! It's a bit of a pain, but now you can RDP into a computer with your Azure AD credentials (aka, email address) to an Azure AD joined computer.

I use a free piece of software called [Remote Desktop Manager](https://remotedesktopmanager.com/) for all my connections. You can't make the necessary changes to a connection in there (that I can tell anyway), but you can create the RDP file using the instructions here then import that connection into the tool and it will work perfectly.
