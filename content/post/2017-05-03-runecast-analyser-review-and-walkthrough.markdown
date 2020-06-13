---
author: Matt Allford
date: 2017-05-03 22:38:05+00:00
draft: false
title: Runecast Analyser Review and Walkthrough
type: post
url: /2017/runecast-analyser-review-and-walkthrough/
categories:
- '2017'
- May
tags:
- Proactive
- Runecase
- Runecast Analyser
- VMware
- vsphere
---

Runecast Analyser was founded in 2014 and at a high level, is a product to provide proactive analysis for VMware environment. I've only recently been introduced to Runecast and I've had the chance to download and use it in my lab environment. I was lucky enough to meet [Ched](https://twitter.com/chedsy00) and [Stanimir](https://twitter.com/sferk) recently at the Melbourne VMUG. I thought I would take the opportunity to record a walkthrough of the product for those that may be interested to see what Runecast Analyser is all about.

Note that the video below was recorded in full HD and is best viewed in 1080P on Youtube.

{{< youtube _j6-J0YGEkg >}}

Check out the video embedded above for a walk through of deployment, configuration and getting started steps with Runecast. The video is based on version 1.5.

Runecast is delivered as a virtual appliance (OVA) that you download and deploy into your vSphere environment. The appliance is light weight, fast to deploy and configure. The interface uses a clean and snappy design built using the HTML5 framework, which makes it a pleasure to use.

Following deployment, among other settings, you configure Runecast to connect with your vCenter Server(s) (multiple vCenter Server supported as of version 1.5) and then configure a schedule for automatic analysis of the environment, or you can of course kick off a manual task to analyse the environment on demand.

After each analysis is complete, Runecast is populated with data from the analysis of the environment and allows administrators to proactively look through the results (or be alerted) and ensure the environment is configured and tuned to avoid known issues, align with best practices and hardening recommendations, resulting in a stable and performant platform.

Runecast Analyser also provides proactive log analysis from ESXi hosts and virtual machines in the environment. After configuring Runecast as a syslog target, which can be automated from within the Runecast Analyser interface or configured interactively or via script (both of which Runecast provides information on how-to), Runecast will accept and analyse logs and proactively alert administrators to any known issues detected within the log files.

As someone who is currently working for a managed services provider, among other things, automation and proactiveness are key. Historically when a new issue was identified, or changes were made to hardening guides, you would have to try to determine which environments for which customers were impacted and act accordingly. Or if you work for internal IT for a company, you had to become aware of the issue, understand if it impacted your environment and then remediate. This method is obviously prone to oversight or error, and that is provided you were even aware of the issue / configuration in the first place. Who's running vSphere 6.5 and has had time to go through the recently released hardening guide in detail? With Runecast Analyser, all of this can be forgotten. With regular updates to ensure the appliance is always in lock step with VMware you can be sure that you are running a healthy environment that aligns with best practices, VMware's security hardening recommendations and that you aren't exposed to any known issues documented in VMware KB articles.

Check out the product at www.runecast.biz and download and deploy the trial in your environment. I think you'll be pleased to see how easy it is to use and the efficiencies Runecast will deliver.

**Please note that this post / video was not sponsored by Runecast and is my own personal thoughts and review of the product**
