---
author: Matt Allford
date: 2017-01-12 11:41:25+00:00
draft: false
title: vSphere 6.5 - VCSA Deployment Walkthroughs
type: post
url: /2017/vsphere-6-5-vcsa-deployment-walkthroughs/
categories:
- '2017'
- January
tags:
- '6.5'
- appliance
- cli
- deployment
- gui
- vcenter
- VCSA
- VMware
- vsphere
---

[toc]


# Introduction


So I've finally had some spare time to download and have a look at some of the vSphere 6.5 components and I'll be planning to do a few blog posts in early 2017 on some of the cool new features that have been added in this release.

I've decided to start with a few posts on the deployment of the vCenter Server Appliance (VCSA) using both the new GUI installer and the CLI option as well. I've put the links immediately below, but I've also written a little about the VCSA below in this article which you may like to read as well. I'll walk through the deployment of an embedded VCSA as well as an external deployment with 1 PSC and 1VC node. The individual posts below include both a video and text with screenshots.

The deployment of the VCSA is now broken into two stages, which can be completed one after another if desired. Stage one walks through the appliance deployment options, where as stage two walks through specific configuration details of the appliance.



* **GUI**
    * [VCSA Embedded Deployment Walkthrough](http://virtualtassie.com/uncategorized/vsphere-6-5-gui-vcsa-embedded-deployment-walkthrough/)
    * VCSA External Deployment Walkthrough (coming soon)
    * VCSA Deploy Additional PSC Walkthrough (coming soon)
* **CLI**
    * [VCSA Embedded Deployment Walkthrough](http://virtualtassie.com/2017/vsphere-6-5-cli-vcsa-embedded-deployment-walkthrough/)
    * [VCSA External Deployment Walkthrough](http://virtualtassie.com/2017/vsphere-6-5-cli-vcsa-external-deployment-walkthrough/)
    * VCSA Deploy Additional PSC Walkthrough (coming soon)



I also highly recommend checking out [William Lam's post](http://www.virtuallyghetto.com/2016/11/vghetto-automated-vsphere-lab-deployment-for-vsphere-6-0u2-vsphere-6-5.html) regarding the automation of VCSA and ESXi for a lab.


## Client Integration Plugin (CIP) deprecation


The CIP was required in vSphere 6 to perform a number of key tasks including deploying the VCSA, File download / upload, some functions within Content Library and also for passing logged in credentials on the vSphere web client login screen.

It has not been smooth sailing and it was a pain point for a lot of people. Thankfully in vSphere 6.5 the CIP has been deprecated, and the VCSA installation / upgrade process has a brand new interface which you will see in the walkthrough posts linked above.


# HTML5 vSphere Client Introduction


For those that weren't aware, in 2016 VMware released and rapidly worked on (releases every week) a new [fling ](https://labs.vmware.com/flings/vsphere-html5-web-client)which was the vSphere Web Client based on HTML5. The interface is very clean and most importantly, performant, especially when compared to the previous web client offering.

With the release of vSphere 6.5, VMware shipped a supported version of the HTML5 client with vCenter Server 6.5 which is fantastic to see. There is limited functionality as development is still in progress, but VMware have stated that they are hoping to release quarterly updates for the production supported version of the HTML5 vSphere Client. The fling will continue to be released on hopefully a weekly cadence.

The HTML5 client can be access to browsing to https://vcenterserverFQDN, where you will find a shortcut, or you can browse directly to https://vcenterserverFQDN/ui/.


## Benefits of VCSA


Since vSphere 6 GA I've been a huge fan of the VCSA and with vSphere 6.5, VMware have really driven the functionality of the VCSA and it is now, in my opinion, easily the best choice for deployment of vCenter. Check our [Emad Younis'](http://emadyounis.com/vcenter/vcenter-server-appliance-vcsa-6-5-whats-new-rundown/) blog post for further information, but the big ticket items (which I may blog on individually later) are:

* **VUM Integration**. This was one of the biggest issues that administrators had with the VCSA in 6, which required them to find a Windows box somewhere to run VUM on. VUM is now fully integrated and deployed by default with VCSA
* **High Availability**. Native HA is now included as an option for the VCSA which makes for a much less complex deployment than other solutions such as load balancing or Microsoft Failover Clustering for those who require HA for vCenter Server
* **Native Backup / Restore**. This is pretty cool and is something I will dive into for sure. VCSA includes a native backup and restore option for both embedded and external deployments
* **Migration to VCSA**. Migration from a Windows vCenter deployment to the VCSA first popped its head as a VMware fling a couple of years ago. It was then released as a supported process in 6.0U2 and again there is support for the process in 6.5. You are able to migrate your windows 5.5/6 vCenter deployments to the VCSA using a wizard that is launched from the VCSA installer. Prior to this, the migration was 'manual' and something I went through on Windows 6 > VCSA 6. Although I used a lot of PowerCLI to automate, it was still quite manual compared to the supported migration and doing it manually also meant some historical event and performance data loss