---
title: "The Windows Search Engine Is Currently Disabled"
date: "2012-01-16"
categories: 
  - "windows"
---

If you have ever gotten this error message you may have been left scratching your head wondering why...kinda like me. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/Windows-Search-Disabled.png)**The Windows Search Engine is currently disabled. Outlook will not be able to provide fast search results using the Instant Search functionality unless the service is running.** Well, like any tech savvy user I went into the Windows features section and found that Windows Search was in fact enabled.  Then I figured, the service may have just stopped running or something happened and it couldn't start properly when I turned the laptop on. So I went to the Windows Services and found to my amazement that it was running just the way it should. Again, being a technical person with fairly good troubleshooting skills my next step was obvious and it worked! [http://www.Google.com](http://www.google.com) That's right, I went to good ol' Google. A couple modified searches later (because the first two searches only brought back people who did have the search service running) I came across this wonderful [blog](http://www.brucebnews.com/2011/04/enable-outlook-2010-search-and-indexing-on-windows-7/). I cannot take credit for coming up with this solution at all. Without trying to explain why each of these things matter, do the following and you'll be good to go...hopefully.

1. In the Windows Features be sure the Windows Search feature is checked (most of the blogs will tell you this, so it's probably the most common fix)
2. While in the Windows Features go ahead and make sure that Indexing Services is checked off too. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/Indexing-Service.png)
3. Finally inside Outlook go to **File** >  **Options** > **Add-Ins** and look under the Disabled Application Add-ins section. If you see Windows Search Email Indexer be sure to enable that. ![](https://images.bradleyschacht.com/wp-content/uploads/2012/01/Windows-Search-Eamil-Indexer-Addin-Disabled.png)

That's about it.  You should be good to go on your awesome email searching functionality.
