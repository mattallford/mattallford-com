---
author: Matt Allford
date: 2016-03-15 03:09:11+00:00
draft: false
title: vCenter Host Profile Core Storage PSP Configuration - Nimble Storage
type: post
url: /2016/march/vcenter-host-profile-core-storage-psp-configuration-nimble-storage/
categories:
- March
tags:
- Host
- Nimble
- NMP
- Profile
- PSP
- SATP
- VMware
---

I'm working on rolling out vCenter Host Profiles at work, to keep an eye on our configuration and also to assist in the deployment of new ESXi servers into our environment. If you haven't checked out host profiles and you have a VMWare Enterprise Plus license, it is a must!

I'm working through a few of our clusters that are connected to our Nimble Storage arrays using the iSCSI Software Initiator, and that use the Nimble Path Selection Policy (PSP) to determine the best path policies from the ESXi host to the storage. The Nimble PSP is installed as a part of the Nimble Connection Manager (NCM).

So, I'd gone ahead and created a new host profile, using the first host in the cluster as the baseline, and then attached the first host back to the profile from which it was created, and it was not compliant. Hmm, I thought that was odd, given I'd JUST created the profile from the host in question, how could it already not be compliant with itself?

Digging down into the details, it was showing some of the PSP configurations as needing to be updated:

[![PSP_1](/wp-content/uploads/2016/03/PSP_1-300x49.png)
](/wp-content/uploads/2016/03/PSP_1.png)

I thought this was odd, so I went back to the host profile and selected edit, so I could have a look at the settings that were being defined in the profile, specifically to do with the PSP. There were two areas of interest:

1. Storage Configuration > Native Multi-Pathing (NMP) > PSP and SATP configuration for NMP devices > PSP configuration for > Device eui.guid

Clicking on the device in the profile showed two configurable options, the Device name and the PSP name:

Device Name: eui.c7eef900c7cc0ad06c9ce9003a6eb93c

PSP name: NIMBLE_PSP_DIRECTED

[![PSP_2](/wp-content/uploads/2016/03/PSP_2-300x81.png)
](/wp-content/uploads/2016/03/PSP_2.png)

2. Storage Configuration > Native Multi-Pathing (NMP) > Path Selection Policy (PSP) configuration > PSP Configuration for > Device eui.guid

Clicking on the device in the profile showed two configurable options, the Device Name and 'Configuration Information':

**Device Name:** eui.c7eef900c7cc0ad06c9ce9003a6eb93c

**Configuration Information:** {policy=iops iops=1 bytes=0 useANO=0 NUM_OF_MEM_ARRAY=1 GROUP_MODE=0 MODE_CHANGED=0 GROUP_ID=0 numIOsForIssueInq=10000 numIOsSinceLastInq=9289 lastPathIndex=0 NumIOsPending=0 numBytesPending=0 }

[![PSP_3](/wp-content/uploads/2016/03/PSP_3-300x53.png)
](/wp-content/uploads/2016/03/PSP_3.png)

These were the only two sections of the host profile that looked to be related to the host not being compliant, so from here I decided to drop into esxcli on the host to see what the details of the PSP for this particular device looked like from there.

I ran the following command to list the Native Multi Pathing (NMP) configuration for the devices that were attached to the ESXi host:

**esxcli storage nmp device list**

I've extracted the output that was specific to the device I've been looking at in the examples above:

```
eui.c7eef900c7cc0ad06c9ce9003a6eb93c
Device Display Name: Nimble iSCSI Disk (eui.c7eef900c7cc0ad06c9ce9003a6eb93c)
Storage Array Type: VMW_SATP_ALUA
Storage Array Type Device Config: {implicit_support=on;explicit_support=off; explicit_allow=on;alua_followover=on; action_OnRetryErrors=off; {TPG_id=0,TPG_state=AO}}
Path Selection Policy: NIMBLE_PSP_DIRECTED
Path Selection Policy Device Config: {policy=iops iops=1 bytes=0 useANO=0 NUM_OF_MEM_ARRAY=1 GROUP_MODE=0 MODE_CHANGED=0 GROUP_ID=0 numIOsForIssueInq=10000 **numIOsSinceLastInq**=3285 lastPathIndex=0 NumIOsPending=0 numBytesPending=0 }
Path Selection Policy Device Custom Config:
Working Paths: vmhba45:C1:T1:L0, vmhba45:C0:T1:L0
Is USB: false
```

The PSP Name looked to be fine - NIMBLE_PSP_DIRECTED, and this matched the host profile settings. But then I noticed there was a setting within the 'Path Selection Policy Device Config', that had a different value from the host profile, and this was the '**numIOsSinceLastInq**', highlighted in bold above. It turns out that this number increments and then resets. I've not yet confirmed what the setting itself it used for, but obviously the host profile had a number that was taken at the time the profile was created, and this is never going to match the configuration of the hosts attached to that profile.

Because of this, I've decided to de-select the 'PSP Configuration for' section of the profile under the PSP configuration. This stopped the configuration being checked and brought the host into compliance:

[![PSP_4](/wp-content/uploads/2016/03/PSP_4-300x272.png)
](/wp-content/uploads/2016/03/PSP_4.png)
