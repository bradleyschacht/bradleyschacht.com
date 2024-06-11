---
title: "PowerPivot Gallery Error Could not load type ‘Microsoft.AnalysisServices.SharePoint.Integration.ReportGalleryView’"
date: "2011-08-02"
categories: 
  - "sharepoint"
---

Oh no you, you got the **Could not load type ‘Microsoft.AnalysisServices.SharePoint.Integration.ReportGalleryView’** error!!!  What to do now? Why did you get it? How to fix it?  All these questions are now answered!

[](https://bradleyschacht.com/powerpivotgalleryerror/powerpivot/)![](https://images.bradleyschacht.com/wp-content/uploads/2011/08/PowerPivotGalleryUnavailable.png)

PowerPivot is a wonderful tool with a lot of power to enable self-service BI in many organizations.  There is a fine line between having users create PowerPivot reports and building an enterprise solution in something like SSAS.  One of the things that blurs that line is sharing PowerPivot reports, sure you could email the files back and forth, but your exchange administrator will probably hate you after sending a couple hundred megabyte file a few times.  Luckily there is a scalable solution between personal and corporate BI: Team BI.  This involves sending the PowerPivot files to a SharePoint site and letting the server do all the lifting of the data processing in the background.  Enough about that and on to the real point of this post. You have PowerPivot all installed and configured; let me first say congrats, that is no small task.  You're very excited to go add a new PowerPivot application to the library when you click on the link and are greeted with a lovely error message (shown above) that reads **Could not load type ‘Microsoft.AnalysisServices.SharePoint.Integration.ReportGalleryView’**.  This is not what you were planning on seeing, am I correct?  Didn't think so.  How do we fix this and why is it happening? **Why it happens:** This particular library type needs an ASP.Net control which can't be found because the code for it is in a dll that is missing.  It is likely occurring because powerpivotwebapp.wsp has not been deployed to the particular Web Application where the site in question is located. **How to fix it:** Luckily the fix isn't too difficult, although it does require that you are an administrator since we need to do some work in Central Administration.  See below for the steps.

1. Open SharePoint 2010 Central Administration from the Start Menu or by typing in the web address in your browser
2. Go to the System Settings section
3. Click on Manage Farm Solutions under the Farm Management section
4. Click on powerpivotwebapp.wsp
5. In the Deployed To section the web application where the error occurred will not be listed
6. Click Deploy Solution at the top of the screen (Shown in the first screenshot below)
7. In the Deploy To section select your web application from the drop down list (Examples in second screenshot below)
8. You have the option to schedule the deployment, keep the Now button selected if you want to be able to use the gallery immediately
9. Click OK
10. Go back to your site and click on the link to load the PowerPivot gallery again, don't just refresh the error page

![](https://images.bradleyschacht.com/wp-content/uploads/2011/08/SolutionPropertiesScreen.png) Image 1: On this screen click **Deploy Solution** in the top left corner. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/08/SolutionDeploymentScreen.png) Image 2: Select your web application from the drop down list and click OK.
