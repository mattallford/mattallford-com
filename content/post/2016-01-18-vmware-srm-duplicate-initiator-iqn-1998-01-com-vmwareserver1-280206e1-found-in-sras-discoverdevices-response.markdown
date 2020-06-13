---
author: Matt Allford
date: 2016-01-18 09:28:28+00:00
draft: false
title: VMWare SRM Duplicate initiator 'iqn.1998-01.com.vmware:Server1-280206e1' found
  in SRA's 'discoverDevices' response.
type: post
url: /2016/vmware-srm-duplicate-initiator-iqn-1998-01-com-vmwareserver1-280206e1-found-in-sras-discoverdevices-response/
tags:
- Initiator
- Nimble
- Replication
- SRA
- SRM
- Storage
- VMware
---

When setting up VMWare SRM 6.1 with array based replication, I was seeing an error after adding the array managers into SRM. The error was **Duplicate initiator 'iqn.1998-01.com.vmware:Server1-280206e1' found in SRA's 'discoverDevices' response.**

In the **vmware-dr-xxx.log** file found in **C:\ProgramData\VMware\VMware vCenter Site Recovery Manager\Logs** there was a tiny bit more info:

```
2016-01-13T09:39:10.757+11:00 [03508 info 'DrTask' opID=5cacf7dd] Task 'dr.storage.ReplicatedArrayPair.discoverDevices112' failed with error: (dr.storage.fault.DuplicateInitiator) {

-->    faultCause = (vmodl.MethodFault) null,

-->    command = "discoverDevices",

-->    responseXml = "<Initiator id="iqn.1998-01.com.vmware:Server1-280206e1" type="iSCSI"/>",

-->    id = "iqn.1998-01.com.vmware:Server1-280206e1",

-->    msg = ""

}
```

The error was being presented by the SRA, so it was something to do with the underlying storage configuration, rather than the host configuation.

In this instance, the underlying storage are Nimble Storage arrays. I went and had a look at how access to the underlying volumes was configured (called initiator groups within the Nimble management interface), and I found the problem.

When managing Nimble storage, you create 'initiator groups' to control access to the LUNs / Volumes, as per any storage device. When using the iSCSI software initiator on the ESXi hosts you will obviously have 1 IQN for the HBA and 2 IP addresses (VM Kernel adapters) if you are set up properly with multipathing. On the Nimble array, we had created 2 entries in the initiator group per host, putting in the details of the IQN and the individual IP address:

| Name      | IQN                                     | IP Address   |
|-----------|-----------------------------------------|--------------|
| server1-1 | iqn.1998-01.com.vmware:Server1-280206e1 | 192.168.1.10 |
| server1-2 | iqn.1998-01.com.vmware:Server1-280206e1 | 192.168.1.11 |

The problem was the fact that we had two entries in the initiator group with the same IQN. We changed the initiator group within the Nimble management interface to have one line per IQN, performed a rescan of the arrays within SRM and everything was OK:

| Name      | IQN                                     | IP Address   |
|-----------|-----------------------------------------|--------------|
| server1-1 | iqn.1998-01.com.vmware:Server1-280206e1 | * |