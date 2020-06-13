---
author: Matt Allford
date: 2017-10-30 11:23:45+00:00
draft: false
title: vRealize Operations Service Discovery Management Pack
type: post
url: /2017/vrealize-operations-service-discovery-management-pack/
categories:
- '2017'
- October
tags:
- infrastructure navigator
- SDMP
- VIN
- VMware
- vrops
---

For those that weren't aware, vRealize Infrastructure Navigator went End of Distribution (EOD) and End of Support Life (EOSL) at the end of September, 2017. Information on this is provided in [another post](https://virtualtassie.com/2017/vmware-infrastructure-navigator-end-of-support/).

If you are not aware of what vRealize Infrastructure Navigator (VIN) was, it was a tool that was part of VMware's vCloud Suite that discovered application dependencies and mapped network flow within a vSphere environment. VIN was deployed as a virtual appliance and then registered with vCenter Server. From there, VIN started mapping application dependencies using VMware Tools and the vCenter Server VIX API.

VIN was quite a handy tool, especially when looking to build Site Recovery Manager plans or NSX security groups.

Because VIN has now gone EOD/EOSL, VMware have released a new tool to take over the functionality of VIN. The new tool is a management pack for vRealize Operations Manager, called vRealize Operations Service Discovery Management Pack, or vRealize Operations SDMP for "short". To quote the [SDMP website](https://marketplace.vmware.com/vsx/solutions/vrealize-operations-service-discovery-management-pack):

```
vRealize Operations Service Discovery Management Pack discovers all the services running in each VM and then builds a relationship or dependencies between services from different VMs, based on the network communication.
The management pack can create dynamic applications based on the network communication between the services and brings in the functionality into VMware vRealize Operations Manager which was earlier provided by VMware vRealize Infrastructure Navigator.
```

Version 2.0 of SDMP was recently released, which was great as version 1 was quite limited in the support. Prior to version 2.0, if you were running vSphere 6.0, you were in a bad place as VIN wasn't working (if your patches were up to date) and SDMP was not supported for vCenter Server 6. Regardless, SDMP 2.0 is compatible with the following:

* VMware vRealize Operations Manager 6.3 or later
* VMware vCenter Server 5.5 or later
* VMware ESXi 5.5 with common user credentials
* VMware ESXi 6.0 or later
* VMware Tools 10.1 or later

SDMP supports integration with SRM, but it doesn't look like it has any integration with NSX. I'm not sure if that will be built in to SDMP or if VMware will rely on vRNI to provide that functionality.

One of the biggest changes between VIN and SDMP is how the tool communicates with guest machines to understand the behaviour and services running in a guest OS. SDMP requires vCenter Guest User Mappings to be configured, else "The vRealize Operations Service Discovery Management Pack cannot discover services on a VM if the guest user mapping is not defined in the vCenter Server".

I've got to admit, when I first read this I was not aware of what Guest User Mappings were, so I came across [this article](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-A38D2EEE-86B3-4440-9617-74065DD7D074.html) on the VMware Docs website. It looks like these were introduced in vSphere 6.0, but in talking to the SDMP developers this feature did sneak in late in 5.5 as well.

In short, this feature allows you to configure a mapping between a vSphere SSO user and a guest OS local administrator account. This mapping is then used by vCenter Server to run commands within the guest operating system. SDMP uses this feature to run commands like wmic in Windows to determine what processes and services are running inside of each guest virtual machine.

When I first read about this, a red flag went up. I essentially need to _provide a vSphere SSO account with access to a local administrator account for **every** guest OS within an environment, which then allows that vSphere SSO user to run commands within the guest operating system_. I also need to articulate this to my customers and get their trust to provide me with local admin credentials. In talking with the developers and product manager of SDMP, this simply is the way it is and it is the method VMware have implemented into vCenter to provide access, now that the VIX API is no longer available for use.

Now that I've pondered on the above for a few weeks, I'm still not sure if this is a huge issue or not from a security and risk perspective. I've only had the chance to have this discussion with one customer, who immediately threw up the red flags as well, but then we talked through what else is at risk if an untrustworthy source had access to a privileged vsphere.local account. Some actions like powering down a VM and copying the VMDK to extract the data can be mitigated with features like VM encryption, so I still do wonder if customers see a big risk in essentially having an avenue to run a command within a guest OS.

I'd like to understand some more about this feature and whether there are legitimate concerns or not (and why not). There seems to be very little information available at this point in time.

Anyhow, User Mappings risk aside, there is still a significant management overhead of SDMP if you ask me, when compared to VIN. Firstly, I need to go through an 'onboarding' process, where I need to create the User Mappings for every single VM within an environment. SDMP does provide a way to do this in bulk via CSV, but I still need to go through and enter a local username and password for every guest OS. Some customers have different local admin account names across flavours or versions of OS. Most customers will also have various appliances which will have a mix of admin / root / whatever for the account name. In any case, the initial creation of User Mappings is not a small task in the typical environment we are responsible for, and there are many environments much larger than those we deal with.

On top of the initial configuration, there are ongoing efforts to maintain User Mappings. Every time a new VM is created, the mapping needs to be created. Whether the new VM is from an admin spinning up a new Linux or Windows server for an application, a developer spinning up a test environment, or the audio visual admin spinning up the latest Polycom appliance from OVA. Errors will be shown in the SDMP tool 'relationship' status if the User Mappings is not complete, and also a warning is logged under tasks and events for the virtual machine if SDMP is trying to query it but doesn't have access.

The article on the VMware Docs website does state that after the initial mapping is complete, subsequent guest management requests use an SSO SAML token to log in to the guest. This means that the password for the local administrator account used to create the User Mappings can change, but access from vSphere to the guest OS will not be impacted.

What are your thoughts on SDMP and the User Mappings required to enable functionality? Are you concerned? Have your customers been concerned, or have they given up guest OS credentials easily? Will you be deploying SDMP in environments that previously had VIN deployed?
