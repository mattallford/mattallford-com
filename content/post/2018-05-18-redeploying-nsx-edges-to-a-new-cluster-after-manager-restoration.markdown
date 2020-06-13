---
author: Matt Allford
date: 2018-05-18 10:26:59+00:00
draft: false
title: Redeploying NSX Edges To a New Cluster After Manager Restoration
type: post
url: /2018/redeploying-nsx-edges-to-a-new-cluster-after-manager-restoration/
tags:
- Edge
- lab
- NSX
- NSX Edge
- Restore
- VMware
---

That title is a bit of a mouthful, but it's Friday night and that's the best I've got!

I was recently working through some recovery scenarios for NSX in the lab, as I was interested to understand what the process was for various scenarios such as:

* Losing NSX Manager and restoring
* Losing NSX Manager, DLR and ESG's and restoring
* Losing the entire cluster (including ESXi hosts) and restoring NSX manager in to a new cluster

The third scenario is reasonably obscure, and I'm not sure if you would follow a different restore process than what I did if this did actually happen. But this is what labs are for, right?

Anyway, this lab was running a nested ESXi 6.5 install with 3 hosts and a vSAN cluster, with NSX 6.4. I deleted the original cluster (simulating some form of failure or loss of the entire cluster) and then recreated the hosts and datastore, created a new cluster object and new vSAN Datastore object (as far as vCenter Server is concerned).

I restored NSX Manager from the backup I took, and interestingly the NSX Edges were showing as deployed, even though they weren't. I tried some resyncing to see if I could get them to NOT show as deployed, but I couldn't make it happen.

[![](/wp-content/uploads/2018/05/NSX_Edge_Restore1.png)
](/wp-content/uploads/2018/05/NSX_Edge_Restore1.png)

I then selected an Edge and clicked on redeploy, but this almost instantly resulted in a 'Configuration Failed' status, as seen below.

[![](/wp-content/uploads/2018/05/NSX_Edge_Restore2.png)
](/wp-content/uploads/2018/05/NSX_Edge_Restore2.png)

Clicking on the 'Configuration Failed' message gives me more information and NSX Manager was telling me the resource pool is not valid, as seen below.

[![](/wp-content/uploads/2018/05/NSX_Edge_Restore3.png)
](/wp-content/uploads/2018/05/NSX_Edge_Restore3.png)

I knew straight away that it is referencing the resource pool (cluster in this case) by the underlying vCenter Managed Object ID rather than the display name or something similar, and this message is fair enough as I'd deleted that resource pool during my destruction.

To remediate this, you can double click on the NSX Edge, go to the **Manage** > **Settings** > **Configuration** screen and then at the very bottom there will be some information on the NSX Edge / Logical Router Appliances (depending on what the object is you are working with).

You can see in my screenshot below that it is referencing MOID's for the datastore and the resource pool (and interestingly enough, it's still showing as deployed on this screen)

[![](/wp-content/uploads/2018/05/NSX_Edge_Restore4.png)
](/wp-content/uploads/2018/05/NSX_Edge_Restore4.png)

To remediate, select the appliance and click the edit button, and then select the correct resource pool and datastore from the options and click on OK. The labels for the resource pool and datastore will return to the display names, and NSX Manager will automatically start redeploying the appliance(s).

[![](/wp-content/uploads/2018/05/NSX_Edge_Restore5.png)
](/wp-content/uploads/2018/05/NSX_Edge_Restore5.png)

After a few minutes, my Edge appliances were back and the configuration was pushed out from NSX manager as expected.
