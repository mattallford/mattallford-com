---
author: Matt Allford
date: 2017-06-06 11:55:32+00:00
draft: false
title: vSphere Host Profile - Could not select an iSCSI HBA for the profile instance
type: post
url: /2017/june-2017/vsphere-host-profile-could-not-select-an-iscsi-hba-for-the-profile-instance/
categories:
- June
tags:
- host profile
- iscsi
- vmhba
- VMware
- vsphere
---

Ahh, vSphere Host Profiles. My old friend.

One of our customers recently had a hardware failure on one of their Dell M620 blade servers, and the server was repaired under warranty which included a motherboard swap. The server got inserted into the chassis and booted back up OK. The server was part of a 12 node cluster, so the customer tried applying the host profile, which would fail when it hit 22%. The servers are running ESXi 6.0U2.

The server is equipped with four hardware dependant iSCSI adaptors via the NDC, and due to the datacenter design with Dell DCB, two of these (VMHBA33 and VMHBA34) are used in this cluster for iSCSI connectivity to an EqualLogic storage array.

I jumped in to have a look. The hostd.log files were not very descriptive in regards to the error, but one of the messages in the host profile compliance view was:

Could not select an iSCSI HBA for the profile instance: <uid>. One of the following may be true: (1) Some of the required user-input host customization settings are missing for the host (2) The system does not have a matching hardware for the given 'Initiator Selection Policy'.

[![](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_1.png)
](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_1.png)

This was repeated four times, for vmhba33 - 36. I checked the customisation settings again when applying the profile, and everything was filled out. I then noticed that the customisation included an iSCSI alias for each adaptor, as well as the MAC address. Given the hardware had been replaced, I decided to do a quick cross check of the current mac address and iSCSI alias of the adaptors, versus what was set in the profile (the profile was still based on the settings prior to the hardware failing).

Here's what was being defined in the profile (settings from the host previous to hardware changes):

[![](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_2.png)
](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_2.png)

And these were the iSCSI aliases / mac addresses from the host port hardware change:

[![](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_4.png)
](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_4.png)

[![](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_3.png)
](/wp-content/uploads/2017/06/HostProfile_iSCSIHBA_3.png)

I cleared out the mac addresses and the iSCSI alias settings from the customisation for vmhba33 - 36, reapplied the profile and left these settings blank (as it picks up these settings from the adaptors automatically), and sure enough the profile applied successfully and the host became compliant after a reboot to apply all of the settings.

There didn't seem to be much around from a search on this particular message, apart from a VMTN post that went unanswered and another forum post where the user just ended up deleting this section of the host profile. Hopefully this helps someone, or at least points you in the right direction if you get the same message.


