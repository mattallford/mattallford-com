---
author: Matt Allford
date: 2018-05-28 10:02:18+00:00
draft: false
title: Vembu Webinar - Why you need Multiple Recovery Options
type: post
url: /2018/vembu-webinar-why-you-need-multiple-recovery-options/
categories:
- '2018'
- May
tags:
- backup
- data protection
- recovery
- Vembu
---

_**Disclaimer:**_ _Vembu are a sponsor of Virtual Tassie and this post is a sponsored blog post. This post has been written in my own words and is not a direct redistribution of a blog post written by Vembu. With that said, I am new to the Vembu suite and have not had time since taking them on as a sponsor to use all aspects of their solution, so some of the content above has been comprised using information from Vembu's website._

In this day and age, we hopefully all understand the important of protecting our data by using backups. With any luck, you also consider data protection across the multiple verticals of your life; mobile data, home media and computers, family devices and of course, enterprise data within the work place.

Having a backup in place is a great starting point. Having a well architected data protection solution with multiple copies of your data is even better. But unless you have multiple recovery options and you are testing these recovery options often, all of your hard work could be for nothing. I've not been in IT all that long, I'm about to hit my 10 year mark actually. But across those 10 years, I have seen and heard first hand of incidents where users needed to recover data and they were unable to do so. There were multiple reasons for this, such as nobody realising this important content wasn't being protected, through to the software saying the data was being protected but for one reason or another it was not recoverable.

This week, Vembu are hosting a Webinar titled "**Why you need Multiple Recovery Options**" highlighting the different recovery options available and how to make the best use of them. You can [Click here](https://register.gotowebinar.com/rt/6804587565098533890) to register. There is also a quick [video overview](https://www.linkedin.com/feed/update/urn:li:activity:6405070308352626688/) describing what to expect with the Webinar.

There are two key factors that get spoken about a lot in the data protection space and these are the key criteria to use when designing and architecting a solution, both of which really need to be driven by business requirements rather than the capability technical solutions. Without these two key metrics defined in your environment for individual systems and the entire IT platform, you are going to have a hard time designing and implementing a data protection solution. They are:

**Recovery Point Objective (RPO)**

In short, the RPO dictates how much data the business is willing to lose in the event of a disaster. This is going to vary between each business. Ideally all business will say "I don't want to lose any data", but at the end of the day if they want this to be true, then they need the budget to back up that statement. Often when businesses undertake a proper analysis of their data, they realise they can in face stand to lose a small portion of data in the event of a disaster (but not always!).

**Recovery Time Objective (RTO)**

The RTO defines the acceptable time to have the systems and services restored following a disaster. In short, it's the accepted down time of the environment. Again, an analysis should be undertaken to understand what impact each system has to the business. What is the cost of this system or service being offline in lost revenue, brand impact / damage, serviceability, and so on. Similar to the RPO, business owners or stakeholder would love to say "we don't want any down time", but even with some of the best architected systems we still see outages in 2018.

As I implied in the opening statement, a backup is only as good as the recovery, meaning the validity of the data and the options provided to you to recover the data in the most suitable manner given the current situation. The section below outlines, at a high level, several of the recovery options that Vembu offers under its [BDR solution suite](https://www.vembu.com/vembu-bdr-suite/).


## **Instant Recovery**


Vembu's Instant Recovery feature helps you to instantly (surprise!) restore your machines (physical or virtual) as a virtual machine during a disaster.

Using the Vembu product, important data can be continuously replicated to another server, synchronously or asynchroonously , and that replicated data can then be used to instantly stand up a virtual machine and attach the replicated data. The data is attached directly from the storage it has been replicated to, so there is no wait time involved with copying the data elsewhere before being able to power on workloads.


## **Permanent Recovery**


The Permanent Recovery feature, also known as full VM recovery, allows you to restore the protected workload, in full, to a destination that makes most sense long term. In other words, maybe you used Instant Recovery to bring workloads online in the smallest timeframe possible, but they may not be running on the optimal hardware.

When you are ready for the workload to be permanently recovered, Vembu provides several data transfer modes to be used to recover the data:


**Direct SAN mode:** If VMware ESXi hosts, datastores, BDR backup server and backup storage targets are deployed in SAN environment, Vembu uses SAN transport mode during restore process and it utilizes FC SAN or iSCSI SAN to transfer the VM data.

**Hot-add mode:** If BDR backup server or [VMBackup](https://www.vembu.com/vembu-vmbackup/) proxy is deployed as a VM on the ESXi host or vCenter server, after initiating the Full VM Restore process, empty disks will be attached with the BDR backup server or VMBackup proxy and VM data will be directly restored to the attached disks. Once VM data is completely restored to the attached disks, it will be then reattached to the recovered VMs.

**Network mode:** If BDR backup server or VMBackup proxy is installed in an isolated environment, the BDR backup server or VMBackup proxy will read the VM data from storage target and then it will be written to the target VMs through LAN connection.




## **File/Folder Recovery**

This option provides you with the option to only protect and recover data that is important to your business.

As an example, you might have a file server that provides multiple shares to end users, but only a handful of the data hosted on this file server might be important. Based on the RPO and RTO requirements, a decision can be made to only protect the important data using the File/Folder method, rather than protecting the entire machine.

One consideration of this method is that you would need to have an alternate machine available to recover the data on to, in the event of a recovery being required.


## **Bare Metal Recovery**


Bare Metal Recovery (BMR) allows you to restore physical machines to the same or new hardware.

The bare metal backup job would include the low level information such as boot information and partition information and can be very valuable in the event of a physical machine issue or failure, especially if you need to restore on to different hardware.


## **Offsite and Cloud Disaster Recovery**


There is a well known data protection rule called the "3-2-1 Rule". The short of it is as follows:

* Have at least **three** copies of your data
* Store the copies on **two** different media
* Keep **one** backup copy offsite

Having your backups stored in the same location as the workloads being protected is a huge flaw in your overall disaster recovery plan. The last component of the "3-2-1 Rule" gets highlighted here - Keep one backup copy offsite - and this is where the Offsite and Cloud Recovery component of the Vembu Suite can assist. Vembu's [Offsite Disaster Recovery](https://www.vembu.com/offsite-disaster-recovery/) helps by replicating the data from the production or primary site, to an offsite location.

If you don't have an offsite location, [Vembu CloudDR](https://www.vembu.com/vembu-clouddr/) could proove to be a handy feature, as this allows your backup data to be replicated to the Vembu Cloud, which is hosted in AWS.

OffsiteDR server component allows organizations to provision a synchronized copy of backup data that is loated in their own offsite DR datacenter. CloudDR allows organizations to utilize Vembu's own cloud environment to house offsite copies of backup data.

Hopefully this post provides a good quick overview of some of the recovery options available with the Vembu BDR suite. To find out more, please register and attend the Webinar later this week!
