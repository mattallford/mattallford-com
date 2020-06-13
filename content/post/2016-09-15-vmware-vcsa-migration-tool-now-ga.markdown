---
author: Matt Allford
date: 2016-09-15 23:15:09+00:00
draft: false
title: VMware VCSA Migration Tool Now GA
type: post
url: /2016/vmware-vcsa-migration-tool-now-ga/
categories:
- '2016'
- September
tags:
- migrate
- migrate2vcsa
- vcenter
- VCSA
- VMware
---

A long last, VMware have made a tool available and supported to migrate from a Windows installation of vSphere 5.5 across to the vCenter Server Appliance (VCSA) 6.0U2. VMware gave us a teaser with a fling in 2015, but this was taken down and there have been hints dropped via Social Media and most recently some good sessions for the migration tool at VMworld 2016.

Prior to this tool, migrating from a Windows install to the VCSA was not a trivial task. I undertook this at my previous employer, where we had 2 vCenter Servers, around 10 clusters and approx 70 hosts. While a lot of it was scripted, it was still a very involved process, as we had distributed switches, a number of cluster, host and VC advanced settings etc. Along with that, the historical data was not transferred across so we had to be OK with not migrating that data.

Some quick notes of some of the questions I've already seen asked a lot (which are answered in the FAQ link below as well):

* Currently this tool is a migration AND upgrade, from 5.5 (any version) to 6.0U2
* This tool does not (yet) help with migrations from a vCenter 6 windows install to the VCSA. We've been told to stay tuned here
* Any supported deployment model of 5.5 is supported with the migration tool, including embedded or external databases
* The download is labelled as 6.0U2M. M meaning migration. You cannot use this download for an upgrade or fresh installation, the only option when starting the wizard is for a migration

I was planning to spin up a 5.5 deployment in my lab and blog the migration process, but the guys at VMware have been all over this out and had the content out straight away when the announcement was made.

So instead, I just wanted to put up a short post with some links off to some helpful resources that are already available.

* [FAQ - READ THESE!](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2146439)
* [VMware vSphere Blog announcement](https://blogs.vmware.com/vsphere/2016/09/vcenter-server-migration-tool-vsphere-6-0-update-2m.html)
* [Release Notes](http://pubs.vmware.com/Release_Notes/en/vsphere/60/vsphere-vcenter-server-60u2m-release-notes.html)
* [vSphere Documentation Center for Migration](http://pubs.vmware.com/vsphere-60/index.jsp?topic=%2Fcom.vmware.vsphere.migration.doc%2FGUID-6B02B2DC-F0DB-4CA3-9944-6BED0CF4B78F.html)
* [Migrate2VCSA Resources by William Lam (bookmark this one)](https://github.com/lamw/migrate2vcsa-resources)

I'll still be spinning it up myself and having a play, this will help us a lot with some customer migrations we have coming up.

Happy migrating!
