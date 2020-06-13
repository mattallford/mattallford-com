---
author: Matt Allford
date: 2017-09-26 10:55:36+00:00
draft: false
title: VMware Skyline
type: post
url: /2017/vmware-skyline/
categories:
- '2017'
- September
tags:
- Proactive
- skyline
- support
- VMware
---

One of the quieter announcements from VMworld US 2017 was that of [VMware Skyline](https://www.vmware.com/support/services/skyline.html). No, not the Nissan sports car!

As a VMware customer, VMware Skyline is a platform that provides VMware visibility in to your environment with the aim of providing proactive and predictive recommendations, based on the configuration in your environment, cross referenced with VMware guidance, KB articles and lessons learned from other customers' environments. In the event that you log a case with VMware, Skyline also provides the Global Support Services (GSS) team access to information about your environment, without needing to spend hours gathering logs, configurations or sitting on a Webex with an Engineer while they get familiar with your environment and specific configurations.

I don't know for sure, but I imagine that from a VMware support perspective, if they look at the data for your environment after a case has been logged, that they will have tools and processes to quickly identify some issues that could be occurring in your environment and contributing to the issue that you logged a case for.

Skyline was the topic for the VMware Communities Roundtable Podcast in Episode #403, which admittedly I have not yet listened to at the time of writing, but it is in my queued list. Talkshoe.com is not currently loading, but I will link to the episode when it is loading, or you can search for it on the popular podcast apps.

Skyline was in the keynote in day one, but you'd be forgiven if you missed it. Skyline is mentioned at around the 25:40 mark:

{{< youtube WTuWxkJe4dM >}}

While at VMworld, I went and spoke to some of the guys on the VMware booth who were familiar with Skyline. We had a look at the product and some of the outputs. One of the outputs was a (very) detailed report covering the customer environment configuration, compliance with the VMware Validated Designs, patching and configuration recommendations, etc. To quote one of the VMware spec sheets:

```
VMware will establish a regular reporting schedule with each customer to discuss observations and insights derived from the ongoing analytics, and to provide prioritized recommendations based on alignment with VMware best practices and VMware Validated Designs. Premier Support (Mission Critical, Healthcare Critical and Business Critical) customers will receive a bi-weekly Proactive Operational Summary Report.
```


With the size of the report I saw, bi-weekly would probably be too often for most customers, unless it was a really high level meeting.

Setup of VMware Skyline is straight forward. At the time of writing, you need to opt in to the program and to do so you need to be a Premier Support customer (Mission Critical Support or Business Critical Support) in North America. After opt in and acceptance, you deploy a collector appliance in to your environment that is the middle man between your environment and VMware's online Skyline repository. VMware have stated Skyline will be available to customers with other production support agreements later in 2018.

If it wasn't apparent yet, "non-identifying" data from your environment will be uploaded to VMware using the collector appliance, which VMware claim is done over an encrypted channel to a secure VMware repository in the US. Being hosted in the US might be good for some, not great for others. I'm sure customers and partners alike will also be interested to understand what data is being captured and sent, and if there is any choice in what is included and any options for additional obfuscation. Obviously the more data you choose to withhold from VMware, should you opt in to using Skyline, the more likely you are to receive less proactive recommendations, less information will be included in the reports and not all of the data may be readily available for GSS in the event of a reactive case being logged. At the end of the day, it will be a trade off decision that each customer should make, and partners will likely need to guide them through this as well to achieve the best outcome.

As of today, Skyline includes the core VMware vSphere components and VMware NSX. Additional products will be added "over time".


## My Own Thoughts


It's great to see VMware jump on the proactive support and analytics train and take it seriously. It's a step in the right direction, but to pull this off I believe that VMware need to put a lot of effort in to this product in the next 6-12 months to get the majority of their offerings included and also open up availability to all customers with production support agreements.

From a partner perspective, It would be fantastic to see VMware show some love here and give customers the option of allowing Partners to be a 'middle man' between Skyline and the customer. Providing a partner portal to Skyline where I could log in and see all of my customers' environments, with summaries of recommendations and proactive guidance would be extremely beneficial. VMware doesn't have the capacity to sit down with each customer and go through what these items may mean for their environments, so let the partners do what they're good at and translate this information for the customers.

I suspect there will be a lot of discussion regarding the privacy of the platform, including the data being gathered from an environment, how it is being transferred to VMware, how and where it is being stored by VMware, and then who can access that data from VMware under which circumstances. Hopefully VMware are working on making this information available in a transparent fashion. Ultimately I do believe that these systems work. I've been a customer and a partner for similar platforms that have been designed well from the ground up and they truly do provide a proactive support experience for customers and partners.

What are your thoughts on Skyline? Let me know in the comments below!
