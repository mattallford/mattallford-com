---
author: Matt Allford
date: 2019-12-27 08:24:38+00:00
draft: false
title: VMware VM Clone via HTML5 Client Results in Incorrectly Configured VMDK
type: post
url: /2019/vmware-vm-clone-via-html5-client-results-in-incorrectly-configured-vmdk/
categories:
- '2019'
- December
tags:
- clone
- html5
- vcenter
- virtual machine
- VMware
- vsphere
---

This one is just a quick post regarding an issue I came up against just before Christmas.

To get straight to the point, I found an issue where in multiple separate environments, when cloning a virtual machine via the VMware vCenter HTML5 Web client interface, if you select the tick box to "customize this virtual machine's hardware" and change any of the settings of the VM, the cloned virtual machine's VMDK file(s) will be pointed to the VMDK file(s) of the source machine you used for the clone.

After some brief testing and logging it with VMware GSS, they are aware of the issue and this appears to only occur on machines that have the VMX configuration disk.enableuuid set to true on the source virtual machine. I was not provided an ETA for a fix, but the current workarounds are:

1. Use vSphere Web Client (FLEX) or PowerCLI to perform the clone operation
2. Edit the Virtual Machine's hardware after cloning the VM
3. Power Off the virtual machine before the Clone task

I'll aim to update this post when it is resolved.
