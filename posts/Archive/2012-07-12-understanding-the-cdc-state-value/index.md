---
title: "Understanding the CDC State Value"
date: "2012-07-12"
categories: 
  - "business-intelligence"
  - "sql-server"
---

If you are using Change Data Capture (CDC) it can be very useful to look at the CDC State table to understand what is happening. I am writing this in the context of using SSIS 2012 CDC components; in this case the CDC Control Task. The CD State can be very helpful to determine why data isn't getting loaded right after an initial load for instance, which is when I decided to dig into the state shown in the state table. The CDC state is used to maintain the state of the loading process. It is used to determine and maintain the processing range when using the CDC components in SSIS 2012. The storage location of the state is indicated as an option in the CDC Control Task and looks similar to this: ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/CDC-State.png) The part of the super simple to decipher string you are wanting to look at is going to be the first part of the state before the "/CS". There are 7 distinct values that may be shown at the beginning of the state field.

- _Empty_ - This is the initial state before any CDC package have been run.
- ILSTART - Initial Load Start - Results from choosing the operation **Mark Initial Load Start** in the CDC Control task. This is the state from the start of the initial load package.
- ILEND - Initial Load End - Results from choosing the operation **Mark Initial Load End** in the CDC Control task.
- ILUPDATE - Initial Load Update - Results from choosing the operation **Get Processing Range** in the CDC Control task while the first run of an update package while the initial load is still in process.
- TFEND - Trickle Feed Update Ended - The final state you would expect to see after a normal and successful CDC update run resulting from choosing the operation **Mark Processed Range** in the CDC Control task.
- TFSTART - Trickle Feed Update Started - The state that occurs when running an update package resulting from choosing the operation **Get Processing Range** in the CDC Control task.
- TFREDO - Reprocessing Trickle Feed Updates - Results from choosing the operation **Get Processing Range**  in the CDC Control task when the previous run did not complete successfully.
- ERROR - umm...Error - There was an error.

Here is what the problem was for me. I set up an initial load package:

1. Mark Initial Load
2. Data Flow
3. Mark Initial End

Then I set up a second package to load updates, and it pulled in NO CHANGES!!!!  What the HECK!!!!

1. Get Processing
2. Data Flow
3. Mark Processed

The issues was if you run an initial load end due to the loading methods of the initial data set CDC thinks that the source database is currently in use (which in my case it was not, it was a static database). So when I ran the update package it went into ILUPDATE mode to pull in whatever updates occurred during the initial load. It went something like this (obviously this is just to recreate what happened, it was two separate packages with data flows between the first and second tasks as well as between the third and fourth tasks): ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/CDC-State-2.png) After running the packages marking the processed range put the status back to ILEND. This again caused the get processing to issue a status change to ILUPDATE on the next run at which point the mark processed finally went to TFEND like I expected. In hind sight this makes sense as it is the way that CDC ensures it doesn't miss any records that were being changed during the initial load, but it was not what I was expecting to see. All subsequent runs result in a TFSTART and TFEND. Since my database source was static I could have just issued a Mark CDC start command and been on my way as shown here: ![](https://images.bradleyschacht.com/wp-content/uploads/2012/07/CDC-State-3.png) To sum up the difference in how those two situations acted here is what when on. **Scenario 1 Setup:** Initial Start Initial End Get Processing Mark Processed **Scenario 1 Outcome:** ILSTART ILEND ILUPDATE ILEND ILUPDATE TFEND TFSTART TFEND Repeat last two outcomes **Scenario 2 Setup:** CDC Start Get Processing Mark Processed **Scenario 2 Outcome:** TFEND TFSTART TFEND TFSTART TFEND Repeat outcome Hopefully this explains in somewhat useful terms what happens in each of those two situations. The best way to understand this is to set up both those demos on your own and run through them. You don't even need to create a data flow in there, you will get great benefit out of just running the different options in the control task by itself. Just be sure to only run the initial start and initial end once to start the process. Running those every time is going to be useless and simulate nothing.
