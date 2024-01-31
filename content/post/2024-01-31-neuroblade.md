---
title: "CFD19 - Accelerating Data Analytics with NeuroBlade"
date: 2024-01-31
author: Matt Allford
draft: false
url: /2024/cfd-19-neuroblade/
categories:
- '2024'
- February

tags:
- CFD
- Cloud Field Day
- Tech Field Day
---

Cloud Field Day #19 kicked off with NeuroBlade, and I'm glad I had a coffee before hand, as data analytics is not my expertise, but I learned a lot in the session thanks to NeuroBlade and other delegates.

> "Revolutionizing Big Data Analytis with NeuroBlade's SQL Processing Unit"

NeuroBlade were founded in 2018, have over 120 employees, and $100m+ in capital funding. A key line they used during their funding round is they "Want to be the NVIDIA of Data Analytics".

The underlying messaging is that CPUs and GPUs aren't built for processing data analytics. Data keeps growing, and accoring to NeuroBladed based on published performance data, a 2x in data growth requires 10x in computer to process, which brings in challenges of scale.

[![](/post/images/data-analytics-acceleration.png)](/post/images/data-analytics-acceleration.png)

# What's Their Solution?

Neuroblade's primary hardware solution is a SQL Processing Unit (SPU), which is purpose built for analytics workloads. It is a standard PCIe card that fits in to any standard server, and Dell are onboard with having it pre-integrated in to their servers as a line item on the BoM when you order a server. They also have a Hardware Enhanced Query System (HEQS), which is a rack mounted server which incorporates NeuroBlade's SPUs.

[![](/post/images/spu-pci-card.png)](/post/images/spu-pci-card.png)

The idea behind the integration is to meet the customer where it's easiest for them to integrate, which is in their analytics software such as Spark, Presto, ClickHouse. Those systems would communicate with the NeuroBlade solution (DAXL) via a set of APIs. DAXL enables queries to be executed on the SPU.

The query is then pushed through to the SPU from the query engine, which offloads and processes the query, and outputs the results back to the query engine. During the session, Elad noted that a recent customer was fully integrated with NeuroBlade within a week, to give an indication on the ease of integration to NeuroBlade's solution from existing infrastructure and systems.

[![](/post/images/neuroblade-integration.png)](/post/images/neuroblade-integration.png)

# So, who is the customer?

My take is their immediate customer profile is those who have large data sets, typically stored and running in their own infrastructure, where they have the relevant access to add PCIe cards in to their data analytics servers to bring NeuroBlade in to their technology stack. Like other "processing units", the goal is to offload the processing on to a dedicated unit, potentially allowing the customer to lower other specifications of the server. We didn't get in to commercials, but I'd like to think the numbers line up to lower the spec of the servers, add NeruoBlade's solution, and receive the performance benefits. Or maybe there's a premium to receiving the offloading performance benefits?

It also seems reasonable that NeuroBlade need to meet the customer where they are, which for a lot of customers means public cloud. I'm not sure it was directly addressed, but I'd imagine NeuroBlade are working with public and private cloud providers to integrate their solution in to the providers' bare metal. The provider can then potentially offer a NeuroBlade based acceleration solution as a SKU to their customers.

# Wrap Up

Data anlytics isn't my forte, but there were delegates in the session that are in this space, and sparked some fantastic discussions and questions.

Based on the data presented by NeuroBlade during the session, I can see and understand the value prop, but I don't work with many customers directly that have the level of scale where NeuroBlade would add a value prop. I'm keen to keey an eye on NeuroBlade and see how their solution progresses.

Make sure to check out the live stream over at the 
[Tech Field Day website](https://techfieldday.com/event/cfd19/)!