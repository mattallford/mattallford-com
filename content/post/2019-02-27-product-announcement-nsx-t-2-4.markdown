---
author: Matt Allford
date: 2019-02-27 08:10:30+00:00
draft: false
title: 'Product Announcement: NSX-T 2.4'
type: post
url: /2019/product-announcement-nsx-t-2-4/
categories:
- '2019'
- February
tags:
- Announcement
- Networking
- NSX
- NSX-T
- SDN
- VMware
---

It's that time of the year again where we have a new NSX release on our hands, specifically NSX-T 2.4. I'm still really new to NSX-T, and I can tell I have _**a lot**_ to learn, but I'm hoping 2.4 is the release I'm able to really start to sink my teeth in with.

If you've been in and around the NSX product, you'll be aware there are two main flavours of NSX; NSX-V (now called NSX for vSphere, I think, or maybe NSX for Data Center(?)) and NSX-T. NSX-V was built around VMware vSphere and has a reliance on vCenter Server. NSX-T is the big brother with many more capabilities, has been build with understanding and support for modern technologies such as containers and multi-cloud, and does not have a reliance on any of the core VMware platform, but of course it does interoperate really well with core VMware technologies. It has been known for a while, but maybe not verbalised, that the effort and development from VMware is heavily focused on NSX-T.

As of NSX-T 2.4, VMware's messaging is that for a greenfield deployment, you should look at NSX-T and not V, or Data Center, or whatever it will be called next week. VMware are communicating that NSX-T now has feature parity (and more) than the V version, so from my 50,000 foot view and understanding, there should be no or very little reason to want to roll with NSX-V for a greenfield deployment.

Some of the highlights of the 2.4 release are list below. Please note that the credit for this list is not mine, and this information is from an NSX vExpert briefing, so thank you to the content producers for sharing!



* **Policy Management**
    * Simplified UI with rick visualisations
    * Declarative Policy API to configure Networking, Security & Services
* **Advanced Network Services**
    * IPv6 (L2, L3, BGP, FW)
    * ENS Support - Edge, DFW
    * VPN (L2, L3)
    * BGP Enhancements
* **Intrinsic Security**
    * Identity-Based Firewall (VDI / RDSH
    * FQDN/URL whitelisting for DFW
    * L7 based application signatures for DFW
    * DFW ops enhancements
* **Security Partner Integration**
    * Guest Introspection
    * E-W Service Insertion
* **Platform Enhancements**
    * Converged NSX Manager appliance with 3-node clustering support
    * Profile based installs, reboot-less maintenance mode upgrades, in-place mode upgrades for vSphere Computer Clusters, n-VDS visualisation, traceflow support for centralised services like Edge Firewall, NAT, LB, VPN
    * v2T Migration. In-built wizards for "vDS to N-vDS" as well as "NSX-v to NSX-T" in place migrations
        * Side note from me, shouldn't this be "forvSphere2T Migration" :D
    * Edge Platform: Proxy ARP support, Bare Metal: Multi-TEP support, In-band management, 25G Intel NIC support



That's a big list of highlights, and there is of course more under the covers which will be in the documentation and release notes.

VMware's announcement blog can be found [here](https://blogs.vmware.com/networkvirtualization/2019/02/introducing-nsx-t-2-4-a-landmark-release-in-the-history-of-nsx.html/).

As usual, please check the [VMware Product Interoperability Matrices](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php) before installing or upgrading when the software is released.

Though based on 2.3, [Jim Streit](https://twitter.com/JimStreit) produced an awesome guide titled "HOW TO BUILD A NESTED NSX-T 2.3 LAB" which you can find [here](https://blogs.vmware.com/services-education-insights/files/2019/02/Nest-Lab-Tech-Doc-2.6.pdf?src=so_5a314d05e49f5&cid=70134000001SkJn) and I hope many of you find useful; I know I will.

No doubt the VMware Hands on Labs (HOL) will be updated soon to host the new 2.4 release as well.

Regarding a migration from DC/V edition to T, there appear to be several ways to achieve this based on what you currently have an what your goal is. I've put a slide below from the vExpert briefing which shows a couple of the scenarios. I'm sure that the superstars in the community will be releasing some content on the considerations for a migration and how best to treat some common scenarios, so make sure to keep an eye out for that. If you're still unsure, make sure you reach out to your VMware rep (AM, TAM, SE, BPM, etc) to get some assistance!

[![](/wp-content/uploads/2019/02/NSX-T_24_MigrationSlide.png)
](/wp-content/uploads/2019/02/NSX-T_24_MigrationSlide.png)

I hope you've enjoyed this quick overview. Let me know in the comments below if you are going to be migrating to T, or are already running T and are planning the upgrade!
