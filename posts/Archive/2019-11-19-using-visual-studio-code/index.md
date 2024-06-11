---
title: "Using Visual Studio Code"
date: "2019-11-19"
categories: 
  - "azure"
  - "scripts"
  - "syndication"
---

I'm a creature of habit. I have my routine when I get to my hotel room on business trips. I have a way I like my email and notes organized. I don't want fruits or veggies on my pizza. And I like tools that I've always used. Don't mess with my tools. I know my shortcut keys in SQL Server Management Studio. I work well in the PowerShell ISE. I know my way around Visual Studio.

Fortunately and unfortunately we live in a time when technology is constantly changing and that means we need tools that are able to adapt and keep up with that change. I've used Visual Studio Code for a while just to edit scripts but never really for my day to day code development. I figured that if I'm going to be [rebuilding my Azure sandbox](https://bradleyschacht.com/building-an-azure-sandbox/), I may as well do it all in PowerShell. Since the PowerShell ISE is no longer being updated I also figured that I should make the move over to Visual Studio Code for as much of my work as I can. Just this week, Microsoft announced that [Visual Studio was going online](https://visualstudio.microsoft.com/services/visual-studio-online/) so this is a perfect time to try and force myself into the VS Code world.

So here I go. Into VS Code. I will try my best to change with the times and move over to new software and new tools that promise a great big beautiful tomorrow. Maybe this will finally get me to use [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download?view=sql-server-ver15) full time (or close to full time) too considering Data Studio is built on top of VS Code.

I'll try to keep this post up to date with the things I'm doing along the way over these couple weeks while I do my sandbox rebuild. It will also be my reference in the future for "what the junk is happening and why can't I do what I want to do?!".

![](https://images.bradleyschacht.com/wp-content/uploads/2019/11/using-visual-studio-code-001.png)

## Thoughts with Brad

I'm in a whole new world. I feel a bit lost right now. Things look pretty. I know this will do everything I want it to do but nothing does quite what I want it to do out of the box. I've gotten far to used to SQL Server Management Studio. Here we go.

I'm noticing many of the shortcuts that I'm used to are there but are just a little different. For instance, Alt + Shift + Up/Down Arrow is now Ctrl + Alt + Shift + Up/Down Arrow.

I may be starting to get the hang of this. I've got a couple extensions. I've got some keyboard shortcuts. I've customized some settings. I still don't feel quite as at home as I do in other tools. For some reason it feels like VS Code isn't quite as polished and as nice of a UI or user experience as other tools. I guess that's what happens when you use a single tool to rule them all though.

I decided to hide the minimap. I don't know that it's super useful for me, but I am sure some will love it. Added that to the settings file.

I'm considering trying out the dark mode for a few days. I generally have my settings for Windows, Visual Studio, Management Studio, on the Light themes that are now shipping. A LOT of people are dark mode fans though, so maybe I'll try it and see if I can get used to it. In the past I haven't really given it a fair shot. We shall see. Not right now though.

Hmmm. This has already come in handy. If you highlight some text then press either " (quote) or ' (apostrophe) on your keyboard it will wrap everything that is highlighted in single or double quotes. Nice little time saver. Nifty.

## Extensions

Extension are proving to be quite useful! I knew I was going to need things like the SQL tooling and PowerShell, but there are a lot of neat extensions out there. I'm hoping that everything just works when I start to do this all in Visual Studio online.

**PowerShell**  
Allows me to develop PowerShell scripts right inside VS Code. Adds PowerShell language support and allows me to debug code right inside VS Code too. I may have to check out PowerShell Pro Tools at some point in the future too as I do miss the module explorer from the PowerShell ISE where it was searchable, showed parameters, and had descriptions. I want to see if the Pro Tools one is any better than the one in the basic PowerShell extension.

**Theme by Language**  
Changes the color theme based on the language selected in a given editor tab. This is really useful for making PowerShell look like PowerShell but have the other tabs use the default theme. This helps me feel at home when I'm writing code across multiple languages at the same time. **Update:** A day or so later I actually got rid of this extension. I think it's an awesome extension and works quite well and if you want to have a more familiar interface by all means use it. I'm trying to go as stock as possible to start with though, and I want to be able to see what my customers are going to see when they fire up the tool and use it. I'll definitely remember this one for the future and will likely add it back in not too far down the road.

**Zoom Bar**  
It's the small things in life that are important. I like having a little icon on the status bar at the bottom of the screen to reset the zoom or select a custom zoom level. This is a minor one that I added because of unfamiliar behavior with the scroll to zoom feature. Not sure if it will stay with me long term or not. The one time I used it may be the only time. I'll reevaluate in a week or so. **Update:** I still have the extension enabled, but I haven't really gotten any use out of it since I found the setting to enable Ctrl + scroll to change the zoom.

**SQL Server (mssql)**  
I'm to the point in my development environment configuration that I need to start doing some SQL work. Where else do I turn but the SQL Server extension. This experience will follow me in some ways over to Azure Data Studio, but for the moment I'm going to stick around in VS Code and kick the tires on this extension to see where it takes me.

## Settings

All the user defined settings are stored in a file called settings.json that you can pull up and easily edit. Alternatively, you can edit the settings through the GUI. This can be edited by pressing Ctrl + Shift + P then typing in Preferences: Open Settings (JSON). If it doesn't come up then you'll need to navigate to the folder where that is stored and open the file.

C:\\Users\\UserNameHere\\AppData\\Roaming\\Code\\User\\settings.json

```
{
    "editor.mouseWheelZoom": true,
    "workbench.colorTheme": "Default Light+",
    "powershell.integratedConsole.focusConsoleOnExecute": false,
    "terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
    "window.menuBarVisibility": "default",
    "workbench.statusBar.visible": true,
    "workbench.activityBar.visible": true,
    "editor.minimap.enabled": false,
    "window.zoomLevel": 0
}
```

## Shortcut Keys

Keyboard shortcuts are stored in a file similar to the settings.

C:\\Users\\UserNameHere\\AppData\\Roaming\\Code\\User\\keybindings.json

**Ctrl + Shift + P**: Show all command

**Ctrl + P**: Go to file

**Ctrl + Alt + Shift + Up/Down Arrow**: Select and insert the same thing on multiple lines at the same time. Big time saver. Interestingly enough, to use the mouse you have to omit the CTRL key. It's ALT + Shift + Mouse Click.

**Ctrl + J**: Hides the lower panel.

**Ctrl + K M:** Change language on current tab. (Ctrl + K, release all keys, then press M)

**F8**: Run currently selected code (On PowerShell after the extension is installed)

**Ctrl + B**: Show or hide the sidebar.
