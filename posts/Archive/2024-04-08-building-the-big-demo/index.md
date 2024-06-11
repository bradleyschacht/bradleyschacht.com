---
title: "Building The Big Demo"
date: "2024-04-08"
categories: 
  - "business-intelligence"
  - "datasets"
  - "microsoft-fabric"
  - "syndication"
---

In the coming weeks I'm going to start building out a brand new demo environment and I'm going to blog the whole thing...probably...and I _plan_ to blog the whole thing!

As you may (or may not) know, I work on the Fabric product team at Microsoft. I am a principal program manager on the Fabric CAT (Customer Advisory Team) team. I always feel strange saying CAT team because then it's team team. It's like trying to explain Microsoft Teams to people. "Just post it on our Teams...uh...team?" or "I need to go to the ATM machine to get cash." You see cash is what people used to use to pay for things before...ok, we are wayyyy off topic. Where was I?

Ah yes, I work on the Fabric CAT team, and I often go talk to people about Fabric as a whole. However, most of the demos I do are just touching on one piece of the puzzle. What I'm setting out to do with this series is show the process of building an end-to-end solution in Microsoft Fabric from data source to visualization showing various options along the way. My goal is to be able to walk into any room and be able to say "let me pull up a workspace a show you what this looks like" for most of the major Fabric functionality.

Let's cover some of the basics...

## What this will not be

Let's get this out of the way first. What will this demo not be, or what purpose will it not be designed to serve.

- This will not be comprehensive of every single piece of functionality in Microsoft Fabric. We are sticking to major themes, the Heading 1 topics if you will.

- This will not include every experience, just the ones I deal with on a regular basis. Think data science or private endpoints. Maybe it will expand in the future, but we'll see where it goes. The idea is big picture, make the concepts simple.

- This will not be complicated where it can be simple. Again, let's not boil the ocean. We aren't hitting every security feature. I'll leave that for individual posts or other people to cover.

- This will not be smoke and mirrors. I will only use what everyone has access to, and I won't hide any code from you to make things look easier than they are or fake any data along the way.

- This will not show every architecture option. I honestly don't know if I am going to show loading data using Dataflows Gen2 since I can do everything I need in a pipeline copy activity. Again, we shall see what happens. Maybe I'll have secondary posts that cover alternative methods or design patterns.

## What this will be

Now that we have some ground rules for what this project will not be, let's go over what it will be.

- This will walk you through every step of building an end-to-end analytics solution on Microsoft Fabric.

- This will be something you can build in your environment as well. I'll make everything available in one form or another, be it screenshots, step-by-step clicks instructions, code on GitHub, or videos on YouTube.

- This will be using the Microsoft Wide World Importers dataset. It's easy to understand (retail sales), has all the translation code to go from the OLTP to DW, and is easily available to everyone.

- This will be as representative of the real world as possible while still keeping the concepts simple. For example, we will use an Azure SQL DB for the source data, but we aren't going to be simulating transactions on the source system and build slowly changing dimension logic. It's easy enough to extend the solution to include those items if you really want to.

- This will be built out over the course of N weeks. That is not a typo, that's not a placeholder I forgot to update, that is the truth. I don't know how long this is going to take. I've thought about doing one post a week, breaking up into approximately 30 minute building sessions, covering one step at a time no matter how long it takes. This is going to change as we go along, I just don't know how long it's going to take. I know I can build everything from scratch in a few hours, but teaching/blogging time is a different animal.

- This will be somewhat sanitized but, as I mentioned above, I won't hide things from you. What I mean by that is that to covert the Wide World Importers into Wide World Importers DW there are a series of views and stored procedures that need to be built/converted. I'm not going to cover every step of that code building/conversion process because it's frankly a bit boring and doesn't serve the purpose of this series, but I will give you all the real code. That means some of the steps you have to do in a real-world project will be happening behind the scenes or will be already done for you. Again, our focus is Fabric not project management or BI development.

## What the architecture will look like

This is still to be defined, but here's what I'm thinking:

**Azure SQL Database** \--- Data Factory Pipeline ---> **Lakehouse Files** \--- Notebook ---> **Lakehouse Tables** \--- T-SQL ---> **Data Warehouse** \--- DirectLake ---> **Power BI report**

Be on the lookout for the first post in the series where we will setup the source database soon!
