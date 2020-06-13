---
author: Matt Allford
date: 2016-06-13 11:28:44+00:00
draft: false
title: ESXi and Nimble Storage Unmap SCSI Blocks
type: post
url: /2016/esxi-and-nimble-storage-unmap-scsi-blocks/
categories:
- '2016'
- June
tags:
- Nimble
- reclaim
- unmap
- VAAI
---

# OverView


Back in vSphere 5 a new VAAI primitive called unmap was released. When using thin provisioned volumes or LUNs for VMFS datastores, actions such as storage vMotion, consolidating snapshots and deleting data (such as VMDK files) do not automatically reclaim the space on the underlying datastore. For example, if you have a thin provisioned 1TB datastore that has 2 x virtual machines using 400GB each, and you delete one of these virtual machines (using the 'delete from disk' action), the used space on the datastore will still report as 800GB.

The unmap VAAI primitive allows administrators of a vSphere environment to run a command from ESXi hosts to reclaim the deleted space. Note that this is in relation to data being reclaimed from the VMFS volume. If data is deleted within a VMDK file, these blocks will not be unmapped by default when running the unmap command on the VMFS volume.

In early builds of either vSphere 5 or 5.1, this action was automated by default, but there were issues with performance, so VMWare made this a manual task.

At work we run Nimble Storage arrays for our virtual infrastructure storage. This article will focus on the unmap command specifically in vSphere 6, as well as using the Nimble Storage Powershell Module to gather statistics of the volume usage before and after running the unmap command. Obtaining the before an after stats and analysing them in excel makes it easy to see how much space has been reclaimed.

It is worth noting that if you use snapshots on your Nimble Storage volumes, you need to wait for the snapshots to age before you will see the space reclamation benefit on your array. If you urgently require the space to be reclaimed, and you are in a position to do so, it might be worth modifying your snapshot schedules to age the data. The powershell function I wrote below collects data by looking at the properties of each volume called "vol_Usage_Compressed_Bytes" and "vol_Usage_Uncompressed_Bytes", so the function can be run directly before and after running the unmap commands and the space savings will be shown in the results.

**Update**: Nick Dyer at Nimble Storage ([@Nick_Dyer_](https://twitter.com/Nick_Dyer_)) put up a [blog post ](https://connect.nimblestorage.com/people/ndyer/blog/2016/07/12/neat-nimbleos-3-feature-vmware-unmap-notifications) to let everyone know that if you are using NimbleOS3 and the your have the VMWare vCenter plugin configured, you will get alerted in the web client when inefficiencies are found and even tells you the command to run from an ESXi host that has access to the datastore. This is a pretty cool feature, and will be even better when we can tweak the projected percentage savings by running the unmap command from the current 20%.


# Verify VAAI Support for unmap


Nimble Storage supports the unmap primitive with VAAI, but you of course may be using a different type of array. To confirm if running the unmap primitive is supported, use the following esxcli command to get the VAAI status of all of the devices connected to a specific ESXi host


`esxcli storage core device vaai status get`


You will want to ensure that you can see "Delete Status: Supported" for the devices of interest.


# Nimble Array Data Gathering


I wanted to be able to show and report on the results of running the unmap command, so I wanted to get the raw statistics from the Nimble array before and after running the unmap command. For this, I looked to the [Nimble Powershell Toolkit](https://connect.nimblestorage.com/community/configuration-and-networking/blog/2016/05/02/introducing-the-nimble-powershell-toolkit-10) which is available for download from Nimble Infosight. The install is straight forward and the required steps are noted in the article I have linked to, so I won't repeat them here.

I wrote a short function, available for download from [GitHub](https://github.com/mattallford/Get-NimbleVolumeUsage), to gather these properties from each volume within a Nimble Storage Group and export them to CSV:

* Volume Name
* Compressed Size in GB
* Uncompressed Size in GB

I will work on this script when time permits and build more features, such as being able to provide an input file with a list of volumes, or specify volumes on a particular array within the group.

As usual with any function, you can copy this into the ISE or a similar editor and execute, and the function will be available for use within your powershell session, or you can save the function into a .ps1 file (such as c:\scripts\Get-NimbleVolumeStorage.ps1) and then 'dot source' it into your powershell session by running the following from the directory where the .ps1 is stored:

    
    . .\Get-NimbleVolumeUsage.ps1


Usage is straight forward, at this stage there are only two parameters (plus the normal built in powershell such as -verbose):

* NimbleGroup - Specify the FQDN or IP address for the management interface of the Nimble Storage Group
* Outputfile - Specify the full path for the OutputFile (CSV)

The function will prompt for credentials to log into the Nimble Storage Group

Example:

    Get-NimbleVolumeUsage -NimbleGroup nimblegroup.lab.allford.id.au -Outputfile c:\scripts\Nimble_Statistics.csv


After running the function, you will have a CSV file with the information for volume usage.


# Unmap Command


It is now time to run the esxcli command to initiate the unmap of data on the datastores. You need to connect to one of your ESXi hosts that has access to the datastores using SSH. The command to run is:


`esxcli storage vmfs unmap -l MyDatastore`


Where '-l MyDatastore' is the volume label as it appears to the ESXi host. You could instead use:


`esxcli storage vmfs unmap -u 509a9f1f-4ffb6678-f1db-001ec9ab780e`


Where -u is the UUID of the datastore.

In our environment, the unmap command took around 30 minutes to run on an 8TB volume located on a Nimble Storage CS500 iSCSI array with 10gig networking. As usual, YMMV.

[This](https://infosight.nimblestorage.com/InfoSight/media/kb/active/htr1455131720138.whz/mfu1456412319802.html) is a link to the Nimble Storage KB article for block reclamation (requires Nimble Infosight login I believe).

[This](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2057513) is a link to the VMWare KB article for running the command on ESXi 5.5 / 6.0 hosts.

[This](https://connect.nimblestorage.com/community/app-integration/blog/2014/03/28/space-reclamation-in-vsphere-55-with-nimble-storage) is a link to a blog post on the Nimble forums that has some extra information for monitoring the datastore when running the unmap command using esxtop.

You can also monitor the hostd/vmkernel log on the ESXi host to see the blocks being unmapped and also see when the task has completed.


# Data Comparison


After running the Unmap command, I ran the powershell function again to gather statistics for space usage on our Nimble Storage volumes (exporting to a different CSV file of course).

I was then able to enter the 'before and after' values for each volume into a Microsoft Excel document, and using the built-in chart builder, I was able to stand up some nice graphs breaking down the space reclamation for the specific volumes, but also to see an overall statistic for space reclaimed on the array. Some of the graphs I came up with are below.

As you can see, we were able to reclaim approx 10TB of compressed space on the CS500 and 2.4TB of compressed space on the CS300. This will of course be different in your environment, as it will depend on what the environment is used for and the rate of storage vMotion / snapshots used / virtual machines deleted.

[![CS500_Volumes_Before_Unmap](/wp-content/uploads/2016/06/CS500_Volumes_Before_Unmap.png)
](/wp-content/uploads/2016/06/CS500_Volumes_Before_Unmap.png)

[![CS500_Volumes_After_Unmap](/wp-content/uploads/2016/06/CS500_Volumes_After_Unmap.png)
](/wp-content/uploads/2016/06/CS500_Volumes_After_Unmap.png)

[![CS500_SCSI_Unmap](/wp-content/uploads/2016/06/CS500_SCSI_Unmap.png)
](/wp-content/uploads/2016/06/CS500_SCSI_Unmap.png)

[![CS300_SCSI_Unmap](/wp-content/uploads/2016/06/CS300_SCSI_Unmap.png)
](/wp-content/uploads/2016/06/CS300_SCSI_Unmap.png)


# Summary


Overall I was interested to see how much space we reclaimed on our Nimble Storage arrays by running the VMFS unmap command. Our arrays have been in production use for approximately 6 months and we will be looking to make the running of the VMFS unmap command a quarterly task.

I was hoping to write a powershell script to automate the process, including gathering the 'before' stats, running the VMFS unmap command and then gathering the 'after' stats, but I had issues with the esxcli command timing out after 30 minutes when called from Powershell. The command was still running and completed OK, but because of the timeout the powershell script itself stopped processing. If anyone knows of any workarounds for this, please let me know.

Remember to check if your array supports the unmap primitive within VAAI (consult with the support team if you are unsure), and my recommendation would be to run the unmap command in a maintenance window, just to be safe.
