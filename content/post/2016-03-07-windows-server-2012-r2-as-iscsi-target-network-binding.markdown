---
author: Matt Allford
date: 2016-03-07 21:39:51+00:00
draft: false
title: Windows Server 2012 R2 as iSCSI Target - Network Binding
type: post
url: /2016/march/windows-server-2012-r2-as-iscsi-target-network-binding/
categories:
- March
tags:
- binding
- iscsi
- server2012r2
- target
---

I'm still getting my lab configured and I am planning on using Windows Server 2012 R2 as an iSCSI target (as well as an NFS server). The server in my environment is called Storage01. I've added 2 NICs to the machine, and was trying to figure out where to set the network binding for the iSCSI traffic. It wasn't where I expected, and I couldn't find much online, so I thought I'd write a quick note on it.

As mentioned, I have 2 NICs on the VM. 1 is for management traffic on 192.168.0.0/24, and the second was bound to my storage network at 192.168.1.0/24. I wanted to limit the iSCSI traffic to the 192.168.1.0 network only.

Using powershell I used Get-IscsiTargetServerSettings to view the settings. I can see that both my adapters with the IP addresses 192.168.0.20 and 192.168.1.10 are listed with a '+', meaning they are active. I couldn't find any parameters in the iSCSI Target cmdlets to change this from powershell. I'm sure there is a way, so when I find it I'll come back to edit the post.

[![iSCSI_NetworkingBinding1](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding1-300x50.png)
](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding1.png)

After a bit of digging around in Server Manager, I found the setting. I originally thought it was going to be under **File and Storage Services** > **iSCSI** > **iSCSI Targets**, but that wasn't the case. If you open server manager and go to **File and Storage Services** > **Server** > **Right click** on the server in question and click on **iSCSI Target Settings**, the network adapter bindings for iSCSI traffic will open.

[![iSCSI_NetworkingBinding2](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding2-300x172.png)
](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding2.png)

In here I was able to uncheck the address on my management network, leaving only the adapter that I want to be used for iSCSI traffic selected. Note the message at the bottom indicating that new network addresses will be automatically enabled, so keep this in mind if you add any additional network configuration to the server.

[![iSCSI_NetworkingBinding3](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding3-300x244.png)
](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding3.png)

Running Get-IscsiTargetServerSettings again I can now see that the only adapter with a '+' is my preferred 192.168.1.10.

[![iSCSI_NetworkingBinding4](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding4-300x53.png)
](/wp-content/uploads/2016/03/iSCSI_NetworkingBinding4.png)
