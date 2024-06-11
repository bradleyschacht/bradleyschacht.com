---
title: "What is Microsoft Fabric"
date: "2023-06-08"
categories: 
  - "business-intelligence"
  - "microsoft-fabric"
  - "syndication"
---

This year at the annual Build developer conference, Microsoft announced the public preview of [Microsoft Fabric](https://www.microsoft.com/en-us/microsoft-fabric), an all-in-one analytics platform that will drive the future of analytics. To say that this release is huge would be an understatement.

On the surface Fabric looks like an iteration on the work that was done when [Azure Synapse Analytics](https://azure.microsoft.com/en-us/products/synapse-analytics/?ef_id=_k_CjwKCAjw1YCkBhAOEiwA5aN4AdhInV-VS2IR5UGSbPGFpIcwr1zMHHwbvkp86woEQ4htL8BlZ72oXBoC_lMQAvD_BwE_k_&OCID=AIDcmm5edswduu_SEM__k_CjwKCAjw1YCkBhAOEiwA5aN4AdhInV-VS2IR5UGSbPGFpIcwr1zMHHwbvkp86woEQ4htL8BlZ72oXBoC_lMQAvD_BwE_k_&gad=1&gclid=CjwKCAjw1YCkBhAOEiwA5aN4AdhInV-VS2IR5UGSbPGFpIcwr1zMHHwbvkp86woEQ4htL8BlZ72oXBoC_lMQAvD_BwE) was launched a few years ago. The reality is that this is not just a step forward but a leap. Many of the components of Fabric do look similar to Synapse, including carrying forward the brand in several of the workloads like Synapse Data Warehousing. However, the architecture, integration, and delivery are vastly different.

![](https://images.bradleyschacht.com/wp-content/uploads/2023/06/what_is_microsoft_fabric_001.png)

## OneLake

We have to start with [OneLake](https://learn.microsoft.com/en-us/fabric/onelake/onelake-overview) because that is the thread that holds this Fabric together. Everyone is going to write, Tweet, or say that so I'm just going to get it out of the way. Please don't hold it against me. I know, low hanging fruit and all, but it is what it is. Moving on... At the core, OneLake is Azure Data Lake Storage but it's so much more than that. Gone are the days of provisioning storage accounts, setting up private endpoints between storage and compute, copying data so different compute engines can access it, managing security in multiple locations, duplicating data when you want to create development environments, and so much more.

You don't create OneLake. You just have OneLake. It's described as OneDrive for your data, which now that I think about it, I already store data in OneDrive...I'll figure that all out another time. The point is, you interact with OneLake just as easily as you interact with OneDrive. It's driven by your Azure AD account by default. It's easy to share with others. You don't have to worry about how to tune to get the necessary IOPS. It's just there.

The best part is that all of the compute engines in Fabric store their data in OneLake, which means all the compute engines can access each other's data. All the architects out there are probably thinking "Does this mean I don't have to do all that data copying to give my SQL people access to the data in my lake? And does that mean I don't have to copy the data from my warehouse to a dataset in Power BI to be able to report on it?" That is absolutely correct. Along with OneLake comes "One Copy" which means every engine can see and interact with the all the data (with proper security of course).

The other best part...or maybe the equally best part, however you'd say that...is OneLake acts just like Azure Data Lake Storage. That means my data is not locked inside OneLake never to be seen again by any of the other Azure services. Therefore, all the Databricks users out there can read and write data to OneLake meaning you can continue to leverage all of those investments and integrations with other services that you've spent years building.

The other, other best part (this is getting out of hand) is if you have an existing Azure Data Lake Storage account you can create a shortcut and bring that data right into OneLake without even having to move it! So everything in your data estate can then be seen and analyzed through OneLake. More on all of this in a future post. As you can see, there is a huge amount of integration that OneLake unlocks and it's arguably the most important piece of the entire puzzle.

## One format

In addition to OneLake there is one other key change that enables all the functionality in Fabric: standardizing on [Delta](https://learn.microsoft.com/en-us/fabric/data-engineering/lakehouse-and-delta-tables). Every compute engine in Fabric now reads and writes Delta format. This is how we prevent the need to copy data from the lakehouse into the warehouse or into the lakehouse from Kusto. Why Delta? At this point, it's the industry standard. Sure, there is some [secret sauce](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order?tabs=sparksql) going on under the covers that enables all kinds of nifty functionality but that's for another time...

## Workloads

Fabric supports a variety of workloads that are more deeply integrated than ever before. We will go into details of each of these workloads in separate posts, but for now let's talk about what is built into the platform.

- [Data Factory](https://learn.microsoft.com/en-us/fabric/data-factory/data-factory-overview) - Easily integrate and transform data from any source. Provides no-code and low-code transformation experiences and continues to be the orchestration hub of the data solution.

- [Synapse Data Engineering](https://learn.microsoft.com/en-us/fabric/data-engineering/data-engineering-overview) - Provides data engineers a familiar, notebook-based experience for transforming data at scale using Spark.

- [Synapse Data Science](https://learn.microsoft.com/en-us/fabric/data-science/data-science-overview) - The place to go for managing, training, and deploying machine learning models.

- [Synapse Data Warehousing](https://learn.microsoft.com/en-us/fabric/data-warehouse/data-warehousing) - A relational data warehouse with truly independent compute and storage that provides industry-leading performance. This is NOT Synapse dedicated SQL pools and it is NOT Synapse serverless SQL pools.

- [Synapse Real Time Analytics](https://learn.microsoft.com/en-us/fabric/real-time-analytics/overview) - Dive into large volumes of data from apps, websites, IoT devices and any other time series data you can get your hands on.

- [Power BI](https://learn.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview) - I feel like this doesn't even need an introduction. Create interactive reports with stunning visuals (I'm not that creative, but I know some people that are) to gain insights from data across your organization.

- Data Activator - Coming soon! - A detection system that provides alerting and monitoring, so you know exactly when important information is changing.

## Experiences

The experience in Fabric line up with the workloads to reduce the noise and quickly surface the most relevant information for what you need to get done.

![](https://images.bradleyschacht.com/wp-content/uploads/2023/06/what_is_microsoft_fabric_002.png)

Selecting an experience, such as Synapse Data Warehouse, will bring you to a screen that shows common tasks and resources that a user working on a data warehouse would find useful. In this case, the ability to create a new warehouse, create a new data pipeline, or link directly to the getting started documentation.

![](https://images.bradleyschacht.com/wp-content/uploads/2023/06/what_is_microsoft_fabric_003.png)

## Software-as-a-Service

Fabric will be delivered like no analytics platform at Microsoft before. Fabric is leveraging a [Software as a Service (SaaS)](https://learn.microsoft.com/en-us/fabric/get-started/microsoft-fabric-overview#saas-foundation) model. This doesn't mean that you have any less functionality, you will still have complete control over your data and experience like you have in all Azure PaaS services. However, it does mean some things are changing for the better including:

- It just works.

- One capacity that will be shared between all the workloads. No more paying for each individual dedicated SQL pool and worrying about paying for dedicated capacity in multiple databases that may sit unused.

- Updates delivered weekly! That means the platform can be updated with the latest and greatest functionality all the time rather than waiting for less-frequent "big bang" releases.

- Trials to get started in seconds if you just want to kick the tires.

- Fast provisioning and automatic scaling. A data warehouse takes about 10-20 seconds to spin up rather than the 10+ minutes that it takes today. Spark pools come online in less than 15 seconds rather than 5+ minutes today.

- Success by default, meaning fewer knobs to tune because the best practices are implemented automatically (for SQL think things like stats **_always_** being up to date) and the workloads are all integrated seamlessly.

## Wrapping up

This is the biggest advance in analytics at Microsoft since I started building data warehouses on SQL Server back in 2009. Full disclosure, I may be a little biased since I work on the Fabric product team, as of the time of writing this post I am a member of Fabric CAT (Customer Advisory Team). Sure, all the core components of this platform existed in one shape or another before, but the level of integration, ease of use, and new functionality that is unlocked with Fabric is quite amazing.

If you want to hear directly from the leadership and feature PM teams about all the different workloads, you can head over to the [Microsoft Fabric Launch Event](https://powerbi.microsoft.com/en-in/blog/microsoft-digital-event-may-24-25/) page to find a list of sessions that were presented on May 24 and 25 showcasing all the functionality that was announced. If you want to just jump right in and watch the full 6+ hours of content, you can catch the on-demand versions for [Day 1](https://www.youtube.com/watch?v=1o_QDFq6gzE) and [Day 2](https://www.youtube.com/watch?v=_Y-XyCRE6ec) over on YouTube.

In a livestream I did earlier this week someone said Fabric was just Synapse repackaged. I can assure you that is not the case. If you have any doubts, just go [try the 60-day free trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial) and see for yourself.
