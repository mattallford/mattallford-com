---
author: Matt Allford
date: 2018-02-27 12:30:21+00:00
draft: false
title: 'VMware Site Recovery Manager: VM Protection - Network Not Configured'
type: post
url: /2018/vmware-site-recovery-manager-vm-protection-network-not-configured/
categories:
- '2018'
- February
tags:
- protection
- site recovery manager
- SRM
- VMware
---

So that title is a mouthful, but I came across an issue for a customer today where network mappings within SRM were not functioning correctly when attempting to configure protection for virtual machines.

To cover this off quickly, the environment is running vSphere 6.0U3 and SRM 6.1.2.

The scenario was that we'd previously (a few months ago) configured all of the mappings in SRM (Network, resource, folder, etc). We then created a new protection group, and created a recovery plan for this particular protection group. This was back when the datastore actually had no virtual machines, so at that point in time there was no VM protections to action, as it was preparing for an upcoming project.

Fast forward to current day, and the customer had migrated quite a few machines on to the datastore which was included in the above protection group, so we wanted to configure SRM protection of the machines.

When attempting to configure protection, the network adapter on all of the virtual machines was showing as "(Not Configured)". The value for Protected Site was empty, and I was being prompted to select the recovery site network. I could manually select the recovery network and the VM protection would complete successfully, but this was not ideal for protecting 70+ machines and there was clearly an issue with the VM protection process retrieving the correct network mapping data.

[![](/wp-content/uploads/2018/02/VM_Protection.png)
](/wp-content/uploads/2018/02/VM_Protection.png)

I scanned the SRM log files and restarted the service, but to no avail. It wasn't critical that these VMs were protected, so I logged it with VMware GSS and uploaded relevant logs.

Long story short, the GSS engineer looked through similar issues internally and suggested we edit the protection group and re-run through the wizard without making any changes. After doing this, protection for all virtual machines in the protection group was configured successfully and the network mappings were being picked up as expected. I should've thought to try an edit of the protection group - one to remember for future for sure.
