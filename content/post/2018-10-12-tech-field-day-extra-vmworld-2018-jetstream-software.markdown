---
author: Matt Allford
date: 2018-10-12 12:38:19+00:00
draft: false
title: Tech Field Day Extra VMworld 2018 - Jetstream Software
type: post
url: /2018/tech-field-day-extra-vmworld-2018-jetstream-software/
categories:
- '2018'
- October
tags:
- blogtober2018
- jetstream
- tech field day
- tfd
- tfdx
---

**DISCLAIMER:  During VMworld 2018 US (Las Vegas), I was invited as a delegate to the Tech Field Day Extra (TFDx) event. I was not compensated in any way, my conference pass was complimentary as I was a speaker at the event and other expenses were covered by my employer, Parallo. I'm not required to write this blog post, I chose to do so of my own accord. This blog post is aimed to summarise the presentation that I was present for and also to provide some additional information about the company or solution presented on, which is all based on my opinion.**

During VMworld 2018 US, [Tech Field Day](https://techfieldday.com/) ran a series of events (known as Tech Field Day Extra, or TFDx) and I was invited to attend several of the sessions as a delegate. Unfortunately due to a jam packed agenda during the week I was only able to attend one, and the one I did attend had 2 vendors presenting that I had not heard of before, so I was really excited to learn more about them.

The first vendor that presented to us was [JetStream Software](https://www.jetstreamsoft.com/).

Who are JetStream? From the JetStream website:

_JetStream Software was founded in 2016._

_The company's management team and software engineering team have worked together for years, starting at FlashSoft Software, which was acquired by SanDisk in 2012. At SanDisk, the team brought leading products to market and worked with VMware as a key design partner._

In a nutshell, JetStream have 3 solutions that are currently available and are all in the area of data, currently focused on VMware environments. The products are called **JetStream Accelerate**, **JetStream Migrate** and **JetStream Data protection**.

JetStream use the VMware IO filter APIs to get access to workload data on a VMware platform without needing to rely on virtual machine snapshots. JetStream are a VMware design partner and are **VMware Ready** certified. In the presentation, Rich noted as far as they are aware, JetStream are the only company to have two shipping products (Accelerate and Migrate) using the IO filter APIs.


# JetStream Accelerate


This was the first solution that JetStream brought to market, which I believe has been available since 2017. We didn't spend much time on this solution in the presentation, but I believe that is because the product itself is reasonably straight forward to understand.

In summary, the aim of JetStream Accelerate is to utilise non-volatile memory installed in the hypervisor host to:
* Provide higher application performance
* Deliver highly consistent service levels
* Increase virtual machine density
* Improve storage efficiency



# JetStream Migrate


JetStream Migrate was my favourite solution from JetStream , and this is the solution that had me most interested as well. JetStream were originally working on their Data Protection solution, but pivoted to finalising and releasing Migrate due to popular demand.

JetStream Migrate is a lightweight application for doing a **one time migration** of workloads between environments. The drive for creating this solution came from the request of cloud providers to ease the onboarding process in to their cloud environment, but the software can also be used for "on-site" migrations as well.

The biggest differentiator for this solution when comparing to others is that all you really need to do is install a VIB on the ESXi hosts. There is no need to spin up servers or infrastructure at either site with heavy management portals or configuring data mover servers or appliances. As already mentioned, the software is using the VMware IO Filter APIs to capture the data, so it is important to note that there are no snapshots being used in this process. Rich noted in the presentation that as far as they are aware, this solution is the lightest footprint they are aware of to achieve the migration.

There is a vCenter plugin where the management of JetStream Migrate is performed. The desintation environment can be selected using Enhanced Link Mode, or by REST API.

The solution can also be placed in to observation mode and will provide an estimated migration time for the selected workloads, based on the data change rate and the network bandwidth available between the source and destination. This is a great feature and one that is often "guesstimated" based on some test migrations, and can often easily be incorrect.

The data can be pre-seeded at the desintation site using physical media. So if you have many TB to transfer in a migration that will take weeks or months over a WAN connection, you can seed the data, ship / drive / fly it to the destination site and then the software will just migrate the deltas across the wire.

There is also the ability to create migration groups, which will provide orchestration and automation of the migration and start up of the workloads in the destination site on migration completion. This is a great feature that provides the flexability to start up servers in the correct order during the migration process to ensure the services are restored with minimal manual intervention in the destination environment.

There were two pricing options mentioned in the presentation:
* Per VM Migrated
* Subscription basis per datacentre destination (for larger service providers)

{{< vimeo 287325670 >}}
{{< vimeo 287325766 >}}


# JetStream Data Protection


We did spend a bit of time in the presentation on the Data Protection solution, which I believe is being released (may be released by the time I finish and publish this post?) in the second half of 2018.

Again, the IO filters are being used here for data capture, so there are no snapshots being taken in this solution. The main feature is the ability to have continual data replication to the cloud for the purposes of data protection and disaster recovery. JetStream support multiple cloud services and use both object based storage for backup purposes as well as cloud based IaaS platforms to facilitate virtual machine failover.

There were some interesting discussions in the presentation regarding the orchestration and automation of bringing up the workloads and doing additional tasks such as changing the IP address of workloads. Make sure to check out the video below to get a bit deeper in to the Data Protection solution and listen to the discussions between delegates and JetStream.

{{< vimeo 287327678 >}}


In summary, I was glad to have attended this particular TFDx session as I hadn't heard about 2 of the 3 vendors presenting in the slot I attended in, and I was certainly intrigued by what JetStream are doing, in particular with the Migration solution. I'm sure I'll be in touch with JetStream soon to dive a bit deeper in to the migration solution and will hopefully find time to do a PoC of the solution as well. What are your thoughts about JetStream and their solution offerings? Let me know in the comment section below!
