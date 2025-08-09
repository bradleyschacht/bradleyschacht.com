---
title: "Azure Data Studio is dead...now what?"
date: "2025-08-12"
tags: 
  - "development"
  - "sql"
  - "syndication"
slug: "azure-data-studio-is-dead-now-what"
cover: "../../media/images/cover-images/generic.png"
---

It was a cold, wet, Tuesday morning. In hindsight I should have known the weather was simply foreshadowing for the harsh reality that would soon confront me. I logged on, opened my web browser, and read the headline that shook me to my core.

<p align="center"><b><i>Azure Data Studio is dead!</i></b></p>

I resisted the move to Azure Data Studio for so long. Why would I need a different tool when I have [SQL Server Management Studio](https://aka.ms/ssms)? SSMS did everything I needed. It was proven. It was solid. But the future, I was told, was Azure Data Studio. After a couple of years I finally decided I was going to jump on the ADS (Azure Data Studio) train during my annaul computer rebuild. I made the decision that I would not be installing SSMS and I would be forcing myself to use ADS.

I typed up about half a blog post, realized it was really more of a complaint post about Azure Data Studio and now I'm starting over. Where were we? Ah yes, I've been using Azure Data Studio as my primary development tool for SQL for several years now. I don't even install SSMS on my computers anymore. It's just not necessary for what I need on a daily basis. When I heard ADS was being retired in favor of the MSSQL extension for VS Code I was sad. I have used the MSSQL extension before and it brought back many of the feelings I had in my early days of switching from SSMS to ADS. It felt unfinished, half baked, and had more rough edges than smooth. It just wasn't ready for prime time. 

Over the last year though there have been quite a few major updates to the extension and I am happy to say, I at the point where VS Code is my daily driver rather than Azure Data Studio. I do feel like there are things that need some help. The result grid for example just feels off. It's a strange mix of missing functionality, clunky rendering, and a fonts that don't match the rest of VS Code. But the core of what I need at this point checks two important boxes: available and functional. 

Now, on to the main point....

How do I configure VS Code's MSSQL extension to be most optimal for my workflow?What settings am I changing to make the environment more functional vs. the defaults? And how can I make VS Code match what I had in Azure Data Studio?

First, you'll need to add the [MSSQL extension](https://learn.microsoft.com/en-us/sql/tools/visual-studio-code-extensions/mssql/mssql-extension-visual-studio-code?view=sql-server-ver17) to VS Code if you haven't already. Next, you'll find that not all settings from Azure Data Studio exist in the VS Code side. 

From there, let's explore my settings file.

``` json
{
    "editor.acceptSuggestionOnEnter": "off",
    "editor.fontSize": 14,
    "editor.minimap.enabled": false,
    "editor.mouseWheelZoom": true,
    "markdown.preview.scrollEditorWithPreview": false,
    "markdown.preview.scrollPreviewWithEditor": false,
    "mssql.copyRemoveNewLine": false,
    "mssql.format.alignColumnDefinitionsInColumns": true,
    "mssql.format.datatypeCasing": "uppercase",
    "mssql.format.keywordCasing": "uppercase",
    "mssql.openQueryResultsInTabByDefaultDoNotShowPrompt": true,
    "mssql.persistQueryResultTabs": true,
    "powershell.integratedConsole.focusConsoleOnExecute": false,
    "security.workspace.trust.untrustedFiles": "open",
    "workbench.colorTheme": "Default Light Modern",
    "workbench.editor.pinnedTabsOnSeparateRow": true,
    "workbench.editor.untitled.labelFormat": "name",
    "workbench.startupEditor": "none",
    "workbench.tree.indent": 20,
    "workbench.tree.renderIndentGuides": "onHover",
    "mssql.connectionGroups": [
        // I have root, Azure SQL Database, and Microsoft Fabric
    ],
    "mssql.connections": [
        // You know I can't give you this information!
    ]
}
```

Of course, your settings will vary from these, but the thing I like about Azure Data Studio and VS Code is everyone can make the editor nearly their ideal editor. For me, I don't really care for intellisense, so turning off accept suggestions on Enter allows me to see intellisense suggestions when I may want to, but doesn't put random items into the editor when I really just want to go to a new line in my code. Here are some thoughts on these settings. 

- Accept Suggestions on Enter: See above.
- Font Size: 14 works for me well across all my computers as I sync these settings between my Surface Laptop and my desktop computer. 
- Minimap: I don't really find a lot of value in this. You can't really see much of anything in it. It's kind of nice when you are searching to see the highlights a little easier, but generally speaking I find it gets in my way.
- Mouse Wheel Zoom: I like to zoom with my mouse wheel. Enough said. 
- Markdown Preview Scroll Editor/Preview: I write my blog posts in markdown, GitHub pages, training docs, and contribute to Microsoft docs. I just don't find the scrolling sync very well and becomes more of a distraction than a help.
- SQL Copy Remove New Line: I want the line feeds to stay in the text I copy.
- SQL Format Options: Just some preference for how I like my SQL code to be formatted. 
- SQL Persist Query Results Tab: When I switch between queries and come back, I want to pick up right where I left off rather than needing to scroll back.
- PowerShell focus console on execute: I run a script and I want to be able to make changes. Stop moving me someplace else!
- Untrusted files: Just let me open my files!
- Color Theme: I like light mode. Don't talk to me about it. Get out of here with your dark mode...unless it's late at night and I'm in my office where it's dark and the light hurts my eyes. But that's temporary, LIGHT MODE FOREVER!!
- Pinned tabs on separate row: Just makes it easier to organize the tabs. I go back and forth on this one. 
- Tab label format: I like to keep a tidy tab. Just the name please.
- Startup editor: Nothing. Give me a blank canvas.
- Tree indent: I like a little more separation between my indentions.
- Tree indent guides: Like my tabs, I like to keep my tree clean. Just show me the guides when I move the mouse over and need to do some navigating. 

I've played around with keyboard shortcuts as well, perhaps I'll go into those details more in another post but the short version is there are some keyboard shortcuts I can't live without (CTRL + E to run a query) but there are some things that I'm trying to rewire my brain to do differently given I use VS Code for more than just SQL development (replacing CRTL + R to hide the results with CTRL + J to toggle the panel).

There are a few more settings and keyboard shortcuts that I had in Azure Data Studio that don't seem to have an equal setting in VS Code or may not be relevant becuase of slight differences in how VS Code is setup. But that's how I have things setup in a nutshell. 

What are you doing for development these days? 

Are you firmly in camp Azure Data Studio for as long as it will run, SSMS forever, already loving VS Code, forcing yourself down a path to get with the times, or perhaps you run a little bit of everything at different times.