---
author: Matt Allford
date: 2017-03-03 10:48:14+00:00
draft: false
title: 'VMware Fling: vSphere Compatibility Predictor'
type: post
url: /2017/vmware-fling-vsphere-compatibility-predictor/
categories:
- '2017'
- March
tags:
- compatibility
- fling
- upgrade
- VMware
- vsphere
---

[toc]


# Introduction


If you work in the VMware suite of products and haven't heard of or checked out the [VMware Flings](https://labs.vmware.com/flings/) website, head on over and check it out! As the website says; "Flings are apps and tools built by our engineers that are intended to be played with and explored."

A lot of products and features that make it into the product start out as being VMware flings. Some recent ones that come to mind are the vCenter Migration Tool (migrate Windows vCenter to the Appliance), HTML5 Web Client, ESXi Embedded Host client ... you get the drift. The flings are not supported by VMware Global Support Services, so the general recommendation is not to use these tools in production environments. The engineers and product managers are usually quite responsive via the comments section on the Flings website and social media.

A few weeks ago, a new fling was released called the [vSphere Compatibility Predictor](https://labs.vmware.com/flings/future-vsphere-compatibility-predictor#summary). At the time of writing, the Fling is at version 1.0.1. This fling connects to a Single Sign On (SSO) node (for 5.5) or Platform Services Controller (PSC) node (for 6.0) in your environment, and from there automatically discovers other PSCs, vCenter Servers and VMware solutions in the environment. It will then give you a high level diagram of your environment, and you can click a button to show "upgrade view". Upgrade view will tell you if the product and version you are running is compatible with vSphere 6.5. Currently it will only show you target compatibility for vSphere 6.5. The engineers have stated in the comments that they are aiming to include other major versions as the target version in the future.

The comments currently state that the fling doesn't detect Log Insight Manager or vRealize Automation. In my testing against vSphere 6, it also did not pick up the Support Assistant Appliance.

Below I'm going to take you through the simple steps to get this running in your environment, and show you what the upgrade view looks like in a small lab environment. I've also got a few of my own thoughts for the fling in the summary.


# Installation and Exploration


First step is to actually download the tool. Head on over to the link above, accept the terms and download the ZIP file. After the ZIP is downloaded, extract and run the EXE inside. This will install the tool onto your system and a shortcut will be created on the desktop.

Go ahead and launch the program from the shortcut

[![](/wp-content/uploads/2017/03/CompatPredictor_1.png)
](/wp-content/uploads/2017/03/CompatPredictor_1.png)

The vSphere Compatibility Predictor will be launched and you will be looking at the screen below. The LDAP port, administrator username and domain are all pre-filled by default. Change the domain if you didn't stick with the default vsphere.local domain. If you are connecting to a vSphere 5.5 SSO node, change the port to 11712.

Enter in the SSO / PSC Node IP/Hostname, as well as the administrator@vsphere.local account password and click on Explore.

[![](/wp-content/uploads/2017/03/CompatPredictor_2.png)
](/wp-content/uploads/2017/03/CompatPredictor_2.png)

If you don't have trusted certificates deployed in your environment, you will be prompted to accept the certificate:

[![](/wp-content/uploads/2017/03/CompatPredictor_3.png)
](/wp-content/uploads/2017/03/CompatPredictor_3.png)

After accepting the certificate, the tool detects the sites in your environment. In my vSphere 6 lab, I have 2 SSO sites within the vSphere domain, as pictured below. Go ahead and click on one of the sites.

[![](/wp-content/uploads/2017/03/CompatPredictor_4.png)
](/wp-content/uploads/2017/03/CompatPredictor_4.png)

Again, if you do not have trusted SSL certs deployed, you will be asked to accept the certs for PSC / VC nodes within the site, pictured below. In this site I have 1 PSC and 1 VC node.

[![](/wp-content/uploads/2017/03/CompatPredictor_5.png)
](/wp-content/uploads/2017/03/CompatPredictor_5.png)

After accepting the SSL certs, some details about my environment are filled out. This includes the versions of PSC / VC nodes, as well as any extensions that are registered with vCenter servers, which were automatically discovered by the tool. In my lab I have vSphere replication and vSphere Data Protection. I do also have the Support Assistant, but this was not detected. I've put this in the comments section of the Fling.

[![](/wp-content/uploads/2017/03/CompatPredictor_6.png)
](/wp-content/uploads/2017/03/CompatPredictor_6.png)

So I've got some detail about what is in my environment, I now want to know what is compatible with vSphere 6.5. Simply click on the Upgrade View button towards the top right. Any products that are compatible will have a green tick, like the one next to the vCenter Server.

Any products that are not compatible will get filled in with red, as picutred for VR and VDP below.

[![](/wp-content/uploads/2017/03/CompatPredictor_7.png)
](/wp-content/uploads/2017/03/CompatPredictor_7.png)

If we check out the [VMware Product Interoperability Matrix](http://partnerweb.vmware.com/comp_guide2/sim/interop_matrix.php?), we can indeed see that VR 6.1.1 and VDP 6.1.0 are not compatible with vSphere 6.5.

[![](/wp-content/uploads/2017/03/CompatPredictor_Interop2.png)
](/wp-content/uploads/2017/03/CompatPredictor_Interop2.png) [![](/wp-content/uploads/2017/03/CompatPredictor_Interop1-300x100.png)
](/wp-content/uploads/2017/03/CompatPredictor_Interop1.png)


# Summary


Put simply, I like this tool. It gives a good quick snapshot of the VMware SSO domain and solutions registered with vCenter Servers within the domain, as well as a quick check for upgrade compatibility. I think this would be extremely helpful for admins who don't often perform upgrades and may even overlook some of the smaller products in their environment, such as Infrastructure Navigator or the Support Assistant.

A few ideas that come to mind for the tool:

* Obviously number one in my mind is to get support for the tool to discover all VMware products
* It would be helpful if the tool could provide information on what version each detected product needs to be upgraded to to become compatible with the target version of vSphere, instead of just showing that the product version isn't compatible and the user having to manually go to the interoperability matrix to review
* I think it would be pretty cool if you could use the tool to generate reports for upgrade paths. For example:
    * One option would be to run this tool in your environment and then export a report of currently unsupported product versions against the target vSphere version, and provide information on what versions the products need to be upgraded to to become compatible
    * Another would be a report that analyses the environment and then provides detail on the suggested upgrade sequence that the administrator should take, based on the KB articles that provide this information such as [KB2147289 ](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2147289)for vSphere 6.5 and [KB2109760](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2109760) for vSphere 6. Given the tool is doing a discovery, it could build out an upgrade plan and also inject the real server / instance names for the administrators environment, making life easier than manually dissecting this from the KB articles



What are your thoughts for this tool? Do you think we'll see this incorporated into the core product soon?
