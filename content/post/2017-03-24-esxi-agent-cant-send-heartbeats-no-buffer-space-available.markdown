---
author: Matt Allford
date: 2017-03-24 01:04:49+00:00
draft: false
title: 'ESXi - Agent can''t send heartbeats: No buffer space available'
type: post
url: /2017/esxi-agent-cant-send-heartbeats-no-buffer-space-available/
categories:
- '2017'
- March
tags:
- buffer space
- error
- esxi
- heartbeats
- VMware
- vsphere
---

Quick post. The other day I saw an alert on an ESXi 6.0 host as follows:

Agent can't send heartbeats: No buffer space available

![](/wp-content/uploads/2017/03/Agent_HEartbeats.png)


The host was responsive and connected to vCenter Server, and the VMs were running OK on the host. I checked the vmkernel / hostd / vpxa logs, and the only mention of this was a one liner in the vmkernel log which repeated the error message above.

The answer was to simply restart the management agents on the ESXi server. This was followed up with VMware GSS afterwards and they confirmed the resolution for this error.

I couldn't find anything online regarding this, so hopefully this helps someone down the track.

Edit: VMware now have a KB on this with some additional info - https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2149738
