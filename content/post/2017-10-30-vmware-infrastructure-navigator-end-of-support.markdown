---
author: Matt Allford
date: 2017-10-30 11:21:25+00:00
draft: false
title: VMware Infrastructure Navigator End of Support
type: post
url: /2017/vmware-infrastructure-navigator-end-of-support/
categories:
- '2017'
- September
tags:
- infrastructure navigator
- SDMP
- VIN
- VMware
- vrops
---

Blink and you will miss it! For those that weren't aware, VMware disclosed a security vulnerability with the vCenter Server VIX API which has been patched, but this also impacted VMware Infrastructure Navigator (VIN) as it used the VIX API to build service mappings.

As per the [VMware KB article](https://kb.vmware.com/s/article/2151075?language=en_US) that announced this information, if you are running vSphere 5.5/6.0 you essentially have two options. Continue using VIN on an unpatched environment (not recommended), or use a new management pack for vROps that replaces VIN functionality called the [Service Discovery Management Pack](https://marketplace.vmware.com/vsx/solutions/vrealize-operations-service-discovery-management-pack). If you are already on vSphere 6.5, VIN will not work so your only option is to use the SDMP.

You can no longer download VMware Infrastructure Navigator, and as of September 26th 2017, VIN is End of Distribution (EOD) and End of Support Life (EOSL).

[I've written an initial post](https://virtualtassie.com/2017/vrealize-operations-service-discovery-management-pack/) on the replacement, SDMP. This isn't a "how to" post or a technical post, though I will likely do one down the track. It's more around the other changes associated with getting SDMP stood up as it isn't quite as straight forward as VIN was.

So, if you have environment with VIN deployed, please be aware that is is now end of life and if you are keeping up to date with your patches, the functionality is now likely broken and VIN isn't working anyway, so start planning the installation and configuration of SDMP.


