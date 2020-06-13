---
author: Matt Allford
date: 2018-11-01 09:07:33+00:00
draft: false
title: Vembu Version 4 - What's Coming
type: post
url: /2018/vembu-version-4-whats-coming/
categories:
- '2018'
- November
tags:
- backup
- Vembu
---

_**Disclaimer:**_ _Vembu are a sponsor of Virtual Tassie and this post is a sponsored blog post. This post has been written in my own words but may contain some material supplied by Vembu for an upcoming release or announcement._

[Vembu](https://www.vembu.com/) have been hard at work behind the scenes on a new version of their [BDR suite](https://www.vembu.com/vembu-bdr-suite/) and version 4.0 is due for release soon.

As a quick recap, the BDR Suite is a range of products available from Vembu to help businesses with data protection and disaster recovery of their workloads. There are several products available under the suite, all of which are managed from a central management portal.

The aim of this post is to outline some of the upcoming features and enhancements with version 4.0 of the BDR Suite, of which hopefully some are beneficial or of interest to you!


# New Features

1. **Hyper-V Cluster Backup**. This is a big feature for Vembu and I'm glad to see this make it in to the product. There will be support for protection of machines in a Hyper-V cluster
2. **Shared VHDx Backup Support**
3. **CheckSum Based Incrementals**
4. **Credential Manager** will help by being able to securely store the credentials of in scope infrastructure

# Enhancements

1. **Vembu ImageBackup Resume**. If an ImageBackup job gets interrupted due to network failure, it will resume from where it left off and not require a restart
2. **Improved Proxy Functionality**.  All backup related requests are triggered by the BDR backup server, the proxy agent will only process the request sent by the BDR Server
3. **Virtual Hardware Selection**. During the live recovery of a machine you can now configure the specs of the machine such as CPU sockets and cores, memory, disk type and network adapter
4. **Improved Hyper-V Live Recovery**. During the recovery of the backed up Hyper-V, a separate agent is pushed to the target machine for performing the full VM recovery
5. **Improved Application Aware Backups for Hyper-V Backups**. For all Hyper-V VMs, you will be able to individually select the VM to which you want to enable application aware processing
6. **Abort Offsite Replication**. It will now be possible to abort the replication job from the Vembu OffsiteDR server
7. **ODR Activation**. You can now activate or deactivate a particular BDR server connected to an OffsiteDR server
8. **Quick VM Recovery Report**. You will now get a report for Quick VM Recovery with details such as VM name, recovery point, start and end times, target hypervisor and status
9. **New APIs**. The new set of APIs are designed to give a detailed report on storage utilisation
10. **VM Disk Inclusion**. Previously after the initial full backup, the newly added disk of the VM is backed up only on the next additional full backup. From 4.0, the newly added disk will be backup in with the successive incremental

I'm keen to get my hands on 4.0 and get it deployed in the lab to have a look through some of the new features, with that said there is a focus above on enablement of Hyper-V functionality and my lab is fully VMware based, but I'm keen to see the release notes to see what else Vembu has managed to fit in to 4.0.

Vembu have a newly published blog post - [Are your servers Highly Available? Check what v4.0 has to offer!](https://www.vembu.com/blog/are-your-servers-highly-available-check-what-v-4-0-has-to-offer/)

And there is an upcoming Webinar with a focus on Hyper-V and the new 4.0 suite functionality, you can sign up [here](https://www.vembu.com/webinars/?utm_source=Analyst&utm_medium=mail).
