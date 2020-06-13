---
author: Matt Allford
date: 2017-12-15 23:17:07+00:00
draft: false
title: 'NSX Controller Deployment Failed: Waiting for VC Lease'
type: post
url: /2017/nsx-controller-deployment-failed-waiting-for-vc-lease/
categories:
- '2017'
- December
tags:
- controller
- NSX
- VMware
---

I was recently rolling out the base install of VMware NSX in my lab, where I ran in to a controller deployment issue. The lab is running NestedESXi, and I have two 'sites' that are nested, each with 3 ESXi hosts in a vSAN cluster. vCenter, PSC and NSX Manager / controllers are then deployed on to the nested ESXi hosts, so the storage is on the nested vSAN datastores.

When deploying the first controller in one of the sites, I ran into a failure. When clicking on the failure, the message I received was "Waiting for VC lease":

[![](/wp-content/uploads/2017/12/NSXController_WaitingForVCLease.png)
](/wp-content/uploads/2017/12/NSXController_WaitingForVCLease.png)

I jumped on to NSX Manager via SSH and watched the log during the attempted controller deployment. I've shortened this down for the purposes of the post, but I believe I have the key components of the log here. The sections where I have **"...."** were several java errors:



```
2017-12-03 22:51:31.235 GMT ERROR taskScheduler-29 VCUtils:247 - Error while waiting for HttpNfcLease updates.
(vim.fault.CannotCreateFile) {
faultCause = null,
faultMessage = (vmodl.LocalizableMessage) [
com.vmware.vim.binding.impl.vmodl.LocalizableMessageImpl@2f41b6e5,
com.vmware.vim.binding.impl.vmodl.LocalizableMessageImpl@7cd56139
],
file = Failed to create directory NSX_Controller_d5e0c004-6730-4d56-94bd-c83c5a67c792 (Cannot Create File)
}

**....**

**....**

2017-12-03 22:51:32.170 GMT INFO DeploymentMonitor DeploymentMonitor:130 - Purge work for finished job jobdata-373 in status: FAILED
2017-12-03 22:51:32.170 GMT INFO DeploymentMonitor DeploymentMonitor:134 - Job jobdata-373 for controller controller-4 failed!
2017-12-03 22:51:32.171 GMT INFO edgeVseMonitoringThread EdgeVseHealthMonitoringThread:279 - Finished Health check for 0 edge vms in 0 millisec
2017-12-03 22:51:32.173 GMT INFO DeploymentMonitor DeploymentMonitor:174 - about to remove controller from database: controller-4 VM id null ip 192.168.36.51 uuid null version 7073587 VSM id 4208D4D9-FE35-BC85-A677-B232384628D2
2017-12-03 22:51:32.174 GMT INFO DeploymentMonitor RelationshipManagerImpl:830 - Recursively removing domain object controller-4 updateParent false
```



Hmm. What stuck out to me here was **(Cannot Create File)**.

I decided to try the NSX controller deployment again, but this time instead of just letting DRS place the deployment, I selected a particular host to try the controller deployment to. This way I could also watch the hostd.log file of the ESXi host during the deployment to see if I could spot anything else.

Sure enough, in the hostd.log file I saw the following - **"Virtual SAN node SiteA-ESXi01.lab.virtualtassie.com maximum Memory congestion reached."**

Here is the full section of hostd.log:

```
2017-12-03T22:51:31.246Z info hostd[2C840B70] [Originator@6876 sub=Solo.Vmomi opID=24c670de-01-01-01-14-0ef2 user=vpxuser:vpxuser] Throw vim.fault.CannotCreateFile
2017-12-03T22:51:31.246Z info hostd[2C840B70] [Originator@6876 sub=Solo.Vmomi opID=24c670de-01-01-01-14-0ef2 user=vpxuser:vpxuser] Result:
--> (vim.fault.CannotCreateFile) {
--> faultCause = (vmodl.MethodFault) null,
--> faultMessage = (vmodl.LocalizableMessage) [
--> (vmodl.LocalizableMessage) {
--> key = "vob.vsanprovider.object.creation.failed",
--> arg = <unset>,
--> message = "Failed to create object.
--> "
--> },
--> (vmodl.LocalizableMessage) {
--> key = "vob.vsan.lsom.congestion",
--> arg = (vmodl.KeyAnyValue) [
--> (vmodl.KeyAnyValue) {
--> key = "1",
--> value = "SiteA-ESXi01.lab.virtualtassie.com"
--> },
--> (vmodl.KeyAnyValue) {
--> key = "2",
--> value = "Memory"
--> }
--> ],
--> message = "Virtual SAN node SiteA-ESXi01.lab.virtualtassie.com maximum Memory congestion reached.
--> "
--> }
--> ],
--> file = "Failed to create directory NSX_Controller_d5e0c004-6730-4d56-94bd-c83c5a67c792 (Cannot Create File)"
--> msg = ""
--> }
```



I went and checked the health of vSAN on this nested cluster, and sure enough it was not in a healthy state! I fixed the underlying issues with vSAN and then tried the NSX controller deployment again, and it worked perfectly. So the cause of the issue was nothing related to NSX itself, as any machine deployment was going to fail with the same issue. the lesson here being to ensure the underlying platform is always healthy before deployment!

With that said, I think the failure messages in the NSX GUI could be much more descriptive to help troubleshoot issues like this faster, but I suspect the current development focus of a product like NSX is on product fixes and enhancements. I wanted to show my process of troubleshooting to narrow down on what "Waiting for VC lease" actually meant, and hell, maybe it will help someone else in the future who is just getting started with NSX like I am.


