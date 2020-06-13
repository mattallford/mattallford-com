---
author: Matt Allford
date: 2020-04-01 20:40:43+00:00
draft: false
title: Update Manager Initiated Reboot Doesn't Trigger Hardware Server Reset
type: post
url: /2020/update-manager-initiated-reboot-doesnt-trigger-hardware-server-reset/
categories:
- '2020'
- March
tags:
- driver
- drivers
- firmware
- hpe
- quickboot
- Update Manager
- VUM
---

This might not be a new one to a lot of folk, but it certainly was to me!

This week I was proactively updating some NIC driver and firmware for a customer to avoid a known issue where the FCoE driver could cause an ESXi Purple Screen of Death (PSOD). Both firmware and drivers needed to be upgraded to maintain interoperability. While I am very much looking forward to [vSphere Lifecycle Manager](https://www.youtube.com/watch?v=R4NGT12hvSM) (finally!) to manage this in a single workflow, for this task I did what I normally would do:

1. Prepare an Update Manager baseline for the drivers
2. Pre-stage the firmware via hardware management to apply on reboot

This has worked for me in the past, as I can simply orchestrate the updating via VUM and on reboot of the host the firmware would apply as well, knocking over both tasks in a single reboot.

I tried that yesterday on the first of the hosts, which are HPE DL380 G10's, but I noticed after the host reboot while the drivers upgraded OK, the firmware did not. After some digging through the logs, the firmware was definitely staged and ready to be applied, but the host itself hadn't registered the reboot that VUM initiated.

After rebooting the host again manually (both a "reboot" command from the shell and a reboot triggered via the vSphere Client seem to work ok), the reset was acknowledged and the firmware was then applied.

In the screenshots below, the first from the vSphere client showing host reboot events and the second from the iLO log, you can see from the tasks logged on the host there are two reset events, but only one, the latter, is recognised in the HPE iLO log. Sorry about the time stamps not lining up in local time in both these screenshots.

![image](/wp-content/uploads/2020/04/2020-04-02_7-10-41.png)


[![](/wp-content/uploads/2020/04/2020-04-02_7-12-15.png)
](/wp-content/uploads/2020/04/2020-04-02_7-12-15.png)



I'm wondering if this is maybe something to do with quick boot, but in summary it doesn't look like a host restart initiated by VUM will register a host restart event with the hardware management software. From the perspective of lifecycle management, hopefully this doesn't actually matter for too much longer. I know I'm really looking forward to getting hands on with the new vSphere Lifecycle Management capabilities in vSphere 7. Be sure to check out the video I linked earlier in this post by [Pete Flecha](https://twitter.com/vpedroarrow) if you haven't already, but if videos aren't your thing, he has also done a neat [blog post](https://blogs.vmware.com/virtualblocks/2020/03/18/part-1-top-new-features-vsan-7-vsphere-lifecycle-manager/) on this topic.
