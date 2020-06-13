---
author: Matt Allford
date: 2016-01-13 20:42:26+00:00
draft: false
title: VMWare SRM Array Based Replication Volume Mounted as 'snap-xxxxxxx-VolumeName'
  After Failover
type: post
url: /2016/srm-array-based-replication-volume-mounted-as-snap-xxxxxxx-volumename-after-failover/
categories:
- '2016'
- January
tags:
- ABR
- Nimble
- Replication
- snap
- SRM
- Storage
- VMware
- Volume
---

I'm going through the process of installing VMWare Site Recovery Manager (SRM) 6.1 in our production environment, which is currently running vSphere 6.0U1.

We use Nimble Storage arrays and have elected to make use of array based replication (ABR) for data replication between sites.

During our initial testing and doing full failovers of some dev applications, I noticed that the datastore name within vCenter for the protected volume on the SAN was getting renamed, and had a prefix of **snap-5b356a02-VolumeName**

SRM doesn't modify the underlying SAN volume / LUN name at all.

You could just go ahead and rename the datastore within vCenter to remove this prefix, but I had a look through the advanced options in the [SRM 6 manual](https://pubs.vmware.com/srm-60/index.jsp?topic=%2Fcom.vmware.srm.admin.doc%2FGUID-E4060824-E3C2-4869-BC39-76E88E2FF9A0.html) and found the following setting:

[![SRM_Snap_Prefix_Fix](/wp-content/uploads/2016/01/SRM_Snap_Prefix_Fix.png)
](/wp-content/uploads/2016/01/SRM_Snap_Prefix_Fix.png)

To implement this advanced setting;

1. Browse to the vSphere Web client and click on the Site Recovery plugin
2. Click on **Sites** > Select your first site > **Manage** > **Advanced Settings** > **Storage Provider **> **Edit**
3. Scroll down to storageProvider.fixRecoveredDatastoreNames and tick the box to enable this setting

Also note that there is another advanced setting related to this, which is **storageProvider.fixRecoveredDatastoreNamesDelaySec** - "Change the time that Site Recovery Manager waits before removing the snap-xx prefix applied to recovered datastore names. The default value is 0 seconds."

Rinse and repeat for any other sites that you have paired within SRM
