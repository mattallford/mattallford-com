---
author: Matt Allford
date: 2017-10-31 10:01:02+00:00
draft: false
title: Emulex Drivers Causing issues with ESXi RamDisk and Scratch Config
type: post
url: /2017/emulex-drivers-causing-issues-with-esxi-ramdisk-and-scratch-config/
categories:
- '2017'
- October
tags:
- emulex
- esxi
- ramdisk
- scratch
---

I'm a bit late to the blogosphere with with one, but we've had a couple of customers hit the issue described below, and it seems to be mostly across HPE and Dell hosts running ESXi 6.x.

I'll get to the good stuff first. If you're hitting an issue on ESXi 6.x where the RamDisk is filling up and **ScratchConfig.CurrentScratchLocation** is reverting to **/scratch**, even with a location configured, it's likely due to a known issue with an Emulex Driver. here are two links to Dell's website which describe the issue and workaround.

[Scratch partition stops working after hardware or software iSCSI is enabled on ESXi with the scsi-be2iscsi Emulex driver](http://www.dell.com/support/manuals/us/en/04/vmware-esxi-6.x/esxi6.xrn_pub/scratch-partition-stops-working-after-hardware-or-software-iscsi-is-enabled-on-esxi-with-the-scsi-be2iscsi-emulex-driver?guid=guid-12321d40-c2a4-4a71-86fd-1104eaa43a54&lang=en-us)

[Scratch partition stops working after hardware or software iSCSI is enabled on ESXi with the elxiscsi Emulex driver](http://www.dell.com/support/manuals/us/en/19/vmware-esxi-6.5.x/esxi6.5.x_rn_pub/scratch-partition-stops-working-after-hardware-or-software-iscsi-is-enabled-on-esxi-with-the-elxiscsi-emulex-driver?guid=guid-0d90f4a1-0a85-459f-ac94-9bc6a1f50f17&lang=en-us)

As noted, we've seen it on some HPE hosts with the HPE ESXi image as well, and there are reports of this on Reddit / VMTN forums as well.

We first noticed the issue when we got some alerts for one of our customers that the Ramdisk on the host was full. This gets logged into hostd.log as well as tasks and events, and likely picked up by tools such as vROps.

```
2017-10-10T08:11:20.805Z info hostd[50981B70] [Originator@6876 sub=Vimsvc.ha-eventmgr] Event 247 : The ramdisk 'root' is full.  As a result, the file /usr/share/vua/vua could not be written.
```

Running `vdf -h` from ESXi shows the Ramdisk usage on the host



```
-----
Ramdisk Size Used Available Use% Mounted on
root 32M 22M 9M 68% --
etc 28M 512K 27M 1% --
opt 32M 212K 31M 0% --
var 48M 476K 47M 0% --
tmp 256M 68K 255M 0% --
iofilters 32M 0B 32M 0% --
hostdstats 1803M 2M 1800M 0% --
```

Though root actually wasn't full, it wasn't far off and this did look a little odd.

For all of our customers, we configure the advanced setting **ScratchConfig.ConfiguredScratchLocation** to a path on a VMFS datastore. What we noticed for these hosts, was that the **ScratchConfig.ConfiguredScratchLocation** was still set correctly, but the advanced setting **ScratchConfig.CurrentScratchLocation** had reverted to /scratch. See the image below for an example.

[![](/wp-content/uploads/2017/10/Scratch_Config.png)
](/wp-content/uploads/2017/10/Scratch_Config.png)

The Configured Scratch Location wasn't applying correctly (normally it required a reboot). This caused the host to start using /scratch, which ultimately led to the issue.

A colleague logged the case with VMware and they investigated, and they've recently pointed us to this KB article - https://kb.vmware.com/kb/2151209, which simply links off to one of the Dell articles for the workaround / resolution.

So if you're facing the issue, check out the links above as this is likely to be the problem. One user on VMTN in the thread below (last post) did mention they upgraded the Emulex driver to 11.4.x which seems to have resolved the issue for them, though there seems to be nothing "official" from VMware, Emulex or vendors yet.

[https://communities.vmware.com/thread/563431?start=15&tstart=0](https://communities.vmware.com/thread/563431?start=15&tstart=0)
