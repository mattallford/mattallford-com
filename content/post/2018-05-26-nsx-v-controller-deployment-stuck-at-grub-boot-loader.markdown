---
author: Matt Allford
date: 2018-05-26 00:13:27+00:00
draft: false
title: NSX-V Controller Deployment Stuck at GRUB Boot Loader
type: post
url: /2018/nsx-v-controller-deployment-stuck-at-grub-boot-loader/
categories:
- '2018'
- May
tags:
- controller
- GRUB
- NSX
- NSX-V
- VMware
- vsphere
---

Recently I was playing around with NSX-V in the lab and getting myself more familiar with the automation options available. During the deployment of one of my environments, I couldn't get the NSX Controllers to come up without failing.

NSX Manager would deploy the controller VM, but it sat in a "deploying" state for around 45 minutes and then failed with a message "no route to host". I checked out the console of one of the controllers directly after deployment and as soon as it booted up it went to the following GRUB boot loader screen:

[![](/wp-content/uploads/2018/05/NSXControler_GRUB.png)
](/wp-content/uploads/2018/05/NSXControler_GRUB.png)



I double checked all of the usual suspects - IP Pools, DNS, NTP, so on a so forth. I put some feelers out on twitter to see if anyone else had come across this before, both [Bayu Wibowo](https://twitter.com/bayupw) and [Mike Da Costa](https://twitter.com/vswitchzero) came back with a few ideas which reminded me I hadn't checked out the underlying storage platform, which in this case was nested vSAN.

{{< tweet 996526109008146433 >}}

{{< tweet 996722983623380999 >}}


I checked out some of the basic stats in the web client of the vSAN datastore, and indeed there was extremely high latency during the periods I was attempting to deploy the controllers:

[![](/wp-content/uploads/2018/05/NSXController_vSANPerf.png)
](/wp-content/uploads/2018/05/NSXController_vSANPerf.png)

I didn't have the time, or patience to be honest, to troubleshoot this too far, so after checking a few basic things I decided to map my physical NFS storage straight through to the nested hosts. After that I performed a redeploy of the controllers on to the NFS based storage and it worked smoothly! Thanks Mike and Bayu for the pointers.

So if you come across this issue with controller deployment, check out your underlying resources to see if there are any issues with the compute or storage layers. I suspect this would almost never be an issue in a production environment.


