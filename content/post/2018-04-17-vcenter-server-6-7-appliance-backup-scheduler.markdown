---
author: Matt Allford
date: 2018-04-17 14:32:34+00:00
draft: false
title: vCenter Server 6.7 Appliance Backup Scheduler
type: post
url: /2018/vcenter-server-6-7-appliance-backup-scheduler/
categories:
- '2018'
- April
tags:
- '6.7'
- appliance
- backup
- vcenter
- VCSA
- VMware
- vsphere 6.7
---

This one will be a quick post, but I wanted to alert people to a new minor but useful feature that's included in the vCenter 6.7 appliance, which is the ability to schedule backups natively within the appliance management interface.

If you wanted to schedule an appliance based backup in 6.5, you would have to schedule a task to do so. Thankfully this was made easier by [Brian Graf's script](https://www.brianjgraf.com/2016/11/18/vsphere-6-5-automate-vcsa-backup/), but now we have a way to build this in to the appliance configuration with no reliance on an external script.

Do note that this is only applicable to the vCenter Server Appliance, but if you haven't yet moved away from the Windows vCenter Server, you really need to do so as this is the last build that will support it.

To set this up is very straight forward, you just need to browse to the VAMI for the VCSA (http://VCFQDN:5480) > Log in > Backup > Configure.

Among the standard settings that are required to set up the backup feature, you will notice a new schedule option is available (highlighted in the screenshot below).

The options for scheduling are daily, weekly, or custom, and the time selected for the backups are based on the local time of the appliance itself.

This is a small but welcome feature, I've seen this requested often since the introduction of the file based backup feature of the VCSA from 6.5.

[![](/wp-content/uploads/2018/04/VCSA_Backup_Scheduler.png)
](/wp-content/uploads/2018/04/VCSA_Backup_Scheduler.png)
