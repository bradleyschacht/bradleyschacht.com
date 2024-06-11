---
title: "Can You Trust Your Cloud?"
date: "2016-08-16"
categories: 
  - "azure"
  - "syndication"
tags: 
  - "azure-trust-center"
  - "compliance"
  - "security"
---

![](https://images.bradleyschacht.com/wp-content/uploads/2016/08/can_you_trust_your_cloud_featured.png) "How do I know my data is secure?" "A lot of companies have been hacked lately, why would I want to move to the cloud?" "We have sensitive data that can't be moved to the cloud." If you are a decision maker in a company you have probably raised questions and objections similar to those above. If you are someone who is architecting solutions that will leverage the cloud you should be asking these questions and finding the answers. I have these types of discussions with customers on a regular basis. There is a lot of uncertainty around the cloud for organizations that are new to the space and rightfully so. If I were talking with a group of data professionals who had never used Microsoft Azure before and they didn't ask me about security I would be concerned. Luckily that doesn't happen often. That brings us to a very important question: can you trust your cloud? To answer that question we need to understand that security comes in two pieces: your architecture and your cloud provider. One you can control, the other has to prove its worth.

> "That brings us to a very important question: can you trust your cloud?"

First, your architecture. This is something the development team is going to control no matter where your applications and data live. No amount of cloud magic is going to secure a database where everyone has sysadmin rights (unless you remove all network access and orphan your VM so that you then can't log into at all and have to delete then rebuild, that may be a little more secure). Whether your applications live on-prem, in the cloud or in a hybrid environment it is important to build it with proper security considerations in place. That means service accounts with the minimum privileges necessary, users that are not members of the sysadmin role because it's easy, encrypting data at rest and in backups...the list goes on. If you can build a secure architecture for your application on-prem then we can help you build that same secure architecture in Azure. Second, your cloud. This is where doing the research and discussing your requirements with someone knowledgeable comes in. If you just have some sales data that contains no customer information and no payment information then the conversation is pretty simple with any cloud provider and you make the call as to whether you think they have data security adequately built into their platform. When it comes to things like medical or PII data then things become a little more interesting. Enter the [Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/). ![](https://images.bradleyschacht.com/wp-content/uploads/2016/08/can_you_trust_your_cloud_1.png) Here are a few bits of information about Azure security. Azure has been built with security as the most important consideration. If people don't trust the Azure platform to secure their data then they will go elsewhere. I can't even begin to tell you everything that Azure has built in from a security perspective. Some of it I feel like I may get fired for blogging about, I'm sure some of it I don't know about and one important thing to keep in mind is that it is ever evolving. From the physical security of the data where every employee has to be background checked and is closely monitored while in the data center to performing real time analytics with [Advanced Threat Analytics](https://www.microsoft.com/en-us/cloud-platform/advanced-threat-analytics) technology you can rest assured your data is in a very secure cloud. Your data can be encrypted a little or a lot. Azure offers encryption options in many services and more are added all the time. If you are using Azure SQL DB then you may choose to employ [transparent data encryption (TDE)](https://msdn.microsoft.com/en-us/library/dn948096.aspx). Using blob storage? Why not take a look at the new encryption features available there. The point is, you can choose where and when your data is encrypted for your solution's needs. We live in a time when passwords alone are not enough. Multi-factor authentication is a requirement to truly secure your environment. Multi-factor authentication implements a requirement for more than just a password. In fact it requires that two of the following be provided before a user can log in: a password, an authentication device like a phone and biometrics. If you are interested in there is a lot of great information in the [Azure Multi-Factor Authentication](https://docs.microsoft.com/en-us/) documentation.

> "Data analytics and machine learning tools to help mitigate threats to the Azure platform."

It's YOUR data and you own it. Always. It doesn't matter what you store or build in Azure that data is yours. In fact the Azure Trust Center goes a step further and ensures there is no ambiguity by saying "You own all your data that you place in Azure—including text, sound, video, or image files and software. You can access your data at any time and for any reason without assistance from Microsoft. We will not use your data or derive information from it for advertising or data mining." The last thing I will discuss here is compliance. Working with government customers this is a big topic of discussion. The same goes for my peers working with health care providers, but the conversation is certainly not limited to just our customers. Azure adheres to a range of international and industry-specific compliance standards. Go to the Trust Center and you will see a huge list of standards. Want to verify the implementation of a particular standard? Sure thing, "you can verify our implementation of many security controls by requesting audit reports from the certifying third parties." What are you looking for CJIS, FedRAMP, HIPAA, IRS 1075, SOC, DSMA? OK, that last one isn't a real thing (that I'm aware of) but you get the idea. To see a full list of certifications head over to the [Compliance](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) section of the Trust Center. If you are specifically interested in CJIS then check our my post [CJIS and the Microsoft Cloud](https://bradleyschacht.com/cjis-and-the-microsoft-cloud/) where I discuss and link to a few recent announcements.

> "Azure meets a range of compliance standards: ISO 27001, SOC, FedRAMP, HIPPA, CJIS and more."

I don't necessarily expect my one blog post to convince you that the Microsoft cloud is secure enough to handle all your data. I fully expect anyone reading this to go do their own research and discuss this topic with your Microsoft reps to dive deeper. I barely scratched the surface of the discussion with a few highlights and only a subset of the compliance standards. I will leave you with this set of highlights that you should be looking for with any cloud provider and urge you to look at the Trust Center to see how Azure can address your concerns around security and compliance. I hope to see some of you soon for an architecture session on how to move and secure your data and applications in Azure.

1. Security is built into the development of Azure. By design, not as an afterthought.
2. Not only does Azure allow for traditional security measures like firewalls but also employs machine learning to identify and mitigate threats, denial-of-service attack prevention and intrusion detection.
3. It is your data and you have control over your data. Always.
4. You know how your data is stored, accessed and how Azure helps to secure it.
5. Compliance is important. Azure conforms to a wide range of global standards across both the commercial and government clouds.