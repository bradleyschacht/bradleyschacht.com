---
title: "Control Flow vs Data Flow"
date: "2009-12-18"
categories: 
  - "business-intelligence"
---

After my first day of SSIS training my boss walked into the office and said to me, "how was the first day of class".  To which I responded, "great!"  Then asked me, "so what is the difference between the control flow and data flow?"  This posed a simple, but foundational concept, of SSIS.  There are a few key things to remember when talking about the differences between control flow and data flow, and not just the completely obvious statement: data flow deals with data.  Hopefully if you are beginning in SSIS there will be something in the information below that will help you understand the differences just a little bit better. Control Flow:

- Process is the key:  precedence constraints control the project flow based on task completion, success or failure
- Task 1 needs to complete before task 2 begins
- Smallest unit of the control flow is a task
- Control flow does not move data from task to task
- Tasks are run in series if connected with precedence or in parallel
- Package control flow is made up of containers and tasks connected with precedence constraints to control package flow

Data Flow:

- Streaming
- Unlink control flow, multiple components can process data at the same time
- Smallest unit of the data flow is a component
- Data flows move data, but are also tasks in the control flow, as such, their success or failure effects how your control flow operates
- Data is moved and manipulated through transformations
- Data is passed between each component in the data flow
- Data flow is made up of source(s), transformations, and destinations.

Hopefully this will help you understand the differences between the control and data flows.
