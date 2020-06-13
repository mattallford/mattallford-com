---
author: Matt Allford
date: 2017-12-05 11:25:24+00:00
draft: false
title: 'VCSA CLI Deployment: Format Requires a Mapping'
type: post
url: /2017/vcsa-cli-deployment-format-requires-a-mapping/
categories:
- '2017'
- December
tags:
- cli
- deployment
- vcenter
- VCSA
- vcsa-deploy
- VMware
---

I've recently been doing a lot of work in my home lab, which has involved me moving from a flat /24 network (yeah, I know) to a somewhat more realistic lab environment with a Cisco SG300 switch and Ubiquiti EdgeRouterx. Due to these networking changes, as I build out the environment I've been creating a lot of VLANs and subnets between my physical ESXi host, the Cisco switch and the ERx.

I regularly use the [CLI deployment method](https://virtualtassie.com/2017/vsphere-6-5-cli-vcsa-external-deployment-walkthrough/) for vCenter and PSC nodes. I really like the ability to spend 2 minutes with a JSON file and then kick off a command to automate stage 1 and stage 2 of the node deployment for me.

Anyway, when spinning up some vCenter Servers in my lab, I came across the following error when it was configuring the services for the first time:

```
Format Requires a Mapping
```

A screenshot of the error from the command line output is below:

[![](/wp-content/uploads/2017/12/VCSA_CLI_FormatRequiresAMapping.png)
](/wp-content/uploads/2017/12/VCSA_CLI_FormatRequiresAMapping.png)

The node failed to configure the services and essentially needs to be redeployed.

The long story short is that this message means there is **_some kind_** of network related issue with your VCSA deployment, where it cannot contact DNS / external PSC / gateway addresses.

I had this occur twice in my lab. The first time was a failure on my part to trunk the new VLAN I was deploying the VM on, to my EdgeRouterx, so the newly deployed VCSA could not contact the gateway address for the network.

The second time I'd just gone through some issues, and to cut a long story short I had to manually move some uplinks on my single physical ESXi host from a distributed switch to a vSwitch using the CLI. I moved the vmnic across, but I forgot to set it as active, so when I deployed the VCSA on this vSwitch, it had no uplink connectivity out of the ESXi host.

So if you come across this message, check any of your network config inside out. Double check the information in your JSON file and make sure your IP addresses, DNS, NTP etc are all correct. If that looks good, take a step back and check the network configuration on the ESXi host you are deploying to. If it's still problematic after that, take another step back to see if there might be a routing issue outside of the ESXi environment.

I hope this helps someone down the track! The message was quite vague, so I thought it was worth a post.
