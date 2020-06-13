---
author: Matt Allford
date: 2019-07-04 05:43:30+00:00
draft: false
title: 'Quick Post: vSphere 6.7 - Sporadic VM Resets by vSphere HA'
type: post
url: /2019/quick-post-vsphere-6-7-sporadic-vm-resets-by-vsphere-ha/
categories:
- '2019'
- July
tags:
- '6.7'
- gss
- reset
- VMware
- vsphere
- vsphere ha
---

We had a customer recently that upgraded from vSphere 6.5 to 6.7 and following the upgrade to 6.7, virtual machines were sporadically being reset by vSphere HA with the following message in the vCenter event log:


```
Error message on <VM Name> on <Host Name> in <Cluster>: VMware ESX unrecoverable error: (vcpu-0) vmk: vcpu-0:Unable to decompress BPN(0x100018003) from frameIndex(0x3d4aa2) block 1 for VM(2103599)
```

We got this logged with GSS quickly and after being sent on a bit of a goose chase with firmware and driver upgrades (the versions we were running we compatible, but not latest), GSS noted that 5 other cases has been logged over the past couple of days with this same issue. Out of the 6 environments, 4 had rolled back to 6.5 and our advice to the customer was to do the same, but they were OK with leaving it and letting GSS try to get to the root cause so it could be fixed.

Some days we would see 2-3 VM resets but sometimes we would go 3 or 4 days without seeing a single reset. There seemed to be no pattern as to which machines were affected or when, the only thing we did observe is that is never occurred twice on a single VM.

After many frustrating weeks trying to get some traction, while VMs were still resetting (we downgraded half the hosts in this cluster and moved critical VMs to hosts running ESXi 6.5), GSS were able to release a hotfix for ESXi that enabled some extra debugging they needed to find the root cause. Thankfully they were able to find root cause which I've copied and pasted below:


```
As discussed, the root cause for the VM crashing  is due to the **zlib **module which was upgraded to 1.2.11 from 1.1.4 in ESXi 6.7. After upgrading there are few issues with  Memory Compression Optimizations. We have changed  the code to re-introduce this memory optimization in ESXi 6.7 U3.

This issue is observed after VM's have been vMotion from 6.5 to 6.7. We have a module dflateInit2() uses the PAGE_SIZE_BITS (12) to compress the page on source (6.5 host) and vMotion, migrates the compressed page to destination. After migration,  we want to decompress the page and see that wbits is 12 in compressed page but now window size greater than 12,it fails and set the status as BAD which returns Z_DATA_ERROR.
```


We asked about getting a hotfix for this issue rather than waiting for U3, but the hotfix was going to likely take longer to build, test and release than U3 will, and it was very likely that if we used the hotfix it would limit upgrade paths in the future.

The customer then agreed to downgrade the rest of the cluster to 6.5 now that root cause had been identified, and when we have interoperability for 6.7U3 confirmed, we'll look at upgrading again.

In the end I was told over 20 customers had logged SRs for this issue since we initially logged ours. I don't think I've seen any other mention of this issue on the web, so I wanted to whip up this post quickly in case anyone else runs in to it. While it doesn't seem to impact every environment, GSS weren't able to give me a defined set of parameters or environmental variables as to when this issue occurs, so with that I'd recommend if you are planning to upgrade to vSphere 6.7 U1 or later (prior to U3), that you do so knowing your environment may be impacted by this issue as well.

**Edit 05 July 2019:** I also confirmed with GSS that this issue is observed when upgrading from any 6.x version to 6.7. The instance above was specifically coming from 6.5 to 6.7, but they have seen this occur in environments upgraded directly from 6.0 to 6.7.

Stay safe out there friends.

**Edit: 18 July 2019**. I wanted to add this paragraph to clear this up a little, and I'm going to use the words of [Nick Bowie](https://twitter.com/nickbowienz) as he summed it up nicely:

"For any VM that experienced memory contention on a 6.x platform, it would have experienced memory compression with a particular version of the zlib module (e.g. 1.1.4 for vsphere 6.5, 1.2.11 for 6.7). That module has an issue when the VM ends up on a 6.7+ host and it tries to decompress the mem pages. Boom, restart. So as part of risk mitigation you could use RVTools and note any VM that shows anything in the mem compression column"

You can use RVTools to look at the vRP tab and see if any resource pools have Compressed memory under the "QS compressedMemory" column, or alternatively there is a short section of PowerCLI code below taken from the [code.vmware.com](https://code.vmware.com/forums/2530/vsphere-powercli#574138) site that will show any virtual machines with compressed memory.

```Powershell
$report = @()

foreach($vm in Get-View -ViewType Virtualmachine){

    $vms = "" | Select-Object VMName,VMHost,Compressed,Ballooned,Swapped

    $vms.VMName = $vm.Name
    $vms.VMHost = Get-View -Id $vm.Runtime.Host -property Name | select -ExpandProperty Name
    $vms.Compressed = $vm.Summary.QuickStats.CompressedMemory
    $vms.Ballooned = $vm.Summary.QuickStats.BalloonedMemory
    $vms.Swapped = $vm.Summary.QuickStats.SwappedMemory
    $Report += $vms
}

$Report | Sort-Object -Property Compressed
```


