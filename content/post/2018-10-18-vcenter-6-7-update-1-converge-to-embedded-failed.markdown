---
author: Matt Allford
date: 2018-10-18 12:48:54+00:00
draft: false
title: vCenter 6.7 Update 1 - Converge to embedded failed!
type: post
url: /2018/vcenter-6-7-update-1-converge-to-embedded-failed/
categories:
- '2018'
- October
tags:
- blogtober
- converge
- vcenter
- VCSA
- vcsa-util
---

**Update 27th October 2018**: I was meant to provide this update last weekend, but it has been a hectic week. Within a few hours of posting this issue, a few people at VMware reached out to me to understand more about my configuration, asked me to upload the logs and a few of the smart guys and gals at VMware had been able to reproduce the issue. As it turns out, the key in the APPLMGMT_PASSWORD vecs store is used when a file based backup of the VCSA is scheduled. I believe a KB is on the way, but the workaround is to delete the VCSA backup schedule, perform the converge and you can then re-enable the schedule after the converge is complete.

**Update 31st March 2019**: This has been out for a little while but I forgot to update this post ... [here](https://kb.vmware.com/s/article/59508) is a link to the VMware KB article for this issue.

So today I was having a play in the lab with the new **vcsa-util** utility in vCenter 6.7 Update 1, that provides the ability to [converge an external PSC and vCenter Server in to an embedded deployment](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vcenter.install.doc/GUID-CE9B6683-D7B0-4717-AD7E-5E89CD069500.html). I've got a walkthrough post on working through this in a basic environment coming soon, stay tuned.

Anyhow, I'd gone ahead and taken my backups, snapshots, got the pre-reqs in place, got my JSON files ready and then I went ahead and ran to the tool to start the converge process.

```
.\vcsa-util.exe converge --backup-taken --no-ssl-certificate-verification --verbose C:\vcsaconverge\sitea-vc01-converge.json
```

After a couple of minutes, the process finished but unfortunately I saw a big FAILED message in the output even though the Precheck validations has succeeded:

```
[01/18] [SUCCEEDED] Precheck validations for converge
[FAILED]Gather requirements
================ [FAILED] Task: MonitorPSCDeployTask: Running MonitorPSCDeployTask execution failed at 05:58:40 ================
Task 'MonitorPSCDeployTask: Running MonitorPSCDeployTask' execution failed because [Error occurred while doing converge operation!], possible resolution is []
================================================================================
Converge to embedded failed!
Downloading /var/log/vmware/converge/converge.log from appliance to local file
C:\Users\matt.LAB\AppData\Local\Temp\vcsaCliInstaller-2018-10-18-05-56-pvhd7cue\workflow_1539842201048\sitea-vc01-converge\converge_mgmt.log
================ [FAILED] Task: MonitorPSCDeployTask: Running MonitorPSCDeployTask execution failed at 05:58:41 ================
Task 'MonitorPSCDeployTask: Running MonitorPSCDeployTask' execution failed
because [ERROR: Converge Process Failed!], possible resolution is []
================================================================================
Error occurred. See logs for details.
Exception message: com.vmware.vcsa.installer.converge.monitor_psc_deploy: ERROR:
Converge Process Failed! Trace:
```

As you can see, there is nothing really helpful here, especially the "**possible resolution is []**", but I guess that's what you get when you run a brand new tool that has been out for a day or so as well.

After getting some time later in the evening when they family were in bed, I had a look at the **converge_mgmt.log** file which is exported to a temporary folder on the machine you are running vcsa-util from (the exact folder is in the output above). Inside of this log file, I found the following error message:

```
2018-10-18T10:13:53.664Z INFO converge Store all vecs certificates.
2018-10-18T10:13:53.926Z ERROR converge Failed to get vecs users and permissions. Error: {
    "resolution": null,
    "problemId": null,
    "componentKey": null,
    "detail": [
        {
            "id": "install.ciscommon.command.errinvoke",
            "translatable": "An error occurred while invoking external command : '%(0)s'",
            "localized": "An error occurred while invoking external command : 'Command: ['/usr/lib/vmware-vmafd/bin/vecs-cli', 'entry', 'getcert', '--store', 'APPLMGMT_PASSWORD', '--alias', 'location_password_default', '--output', '/root/velma/old_certs/APPLMGMT_PASSWORD.crt']\nStderr: Error: No certificates were found for entry [location_password_default] of type [Secret Key].\nvecs-cli failed. Error 87: Operation failed with error ERROR_INVALID_PARAMETER (87) \n'",
            "args": [
                "Command: ['/usr/lib/vmware-vmafd/bin/vecs-cli', 'entry', 'getcert', '--store', 'APPLMGMT_PASSWORD', '--alias', 'location_password_default', '--output', '/root/velma/old_certs/APPLMGMT_PASSWORD.crt']\nStderr: Error: No certificates were found for entry [location_password_default] of type [Secret Key].\nvecs-cli failed. Error 87: Operation failed with error ERROR_INVALID_PARAMETER (87) \n"
            ]
        }
    ]
}
2018-10-18T10:13:53.936Z INFO converge Cleanup successful with partial flag = True.
```

I took the "args" section of that output and look at the command that was trying to be run, which ended up being the following:

```
/usr/lib/vmware-vmafd/bin/vecs-cli entry getcert --store APPLMGMT_PASSWORD --alias location_password_default
```

I opened an SSH session to the vCenter Server I was trying to converge and ran the above command, and sure enough I got the same output that was being shown in the converge_mgmt.log file:

```
root@root-vc01 [ ~ ]# /usr/lib/vmware-vmafd/bin/vecs-cli entry getcert --store APPLMGMT_PASSWORD --alias location_password_default
Error: No certificates were found for entry [location_password_default] of type [Secret Key].
```

I then used vecs-cli to list the entries in this specific store, getting the response below:
```
root@root-vc01 [ ~ ]# /usr/lib/vmware-vmafd/bin/vecs-cli entry list --store APPLMGMT_PASSWORD
Number of entries in store :    1
Alias : location_password_default
Entry type :    Secret Key
Certificate :
```



So I could see there was not a certificate in the store, but there was a key.

Now, I do not know what this store is used for, so if you know I'd love if you can leave me a comment below, or reach out to me on twitter @mattallford. I checked a couple of other 6.7 environments, and a few 6.5 ones, and also spun up a clean external 6.7U1 environment in my lab, and this store was empty on all of them. So provided this was a lab environment, I decided to try and remove the entry from the store on the vCenter Server using the following command:

``` 
/usr/lib/vmware-vmafd/bin/vecs-cli entry delete --store APPLMGMT_PASSWORD --alias location_password_default


Warning: This operation will delete entry [location_password_default] from store [APPLMGMT_PASSWORD]
Do you wish to continue? Y/N [N] y
Deleted entry with alias [location_password_default] in store [APPLMGMT_PASSWORD] successfully
```



After deleting the entry from the vecs store, I was able to rerun vcsa-util again from my management machine and this time the converge process completed successfully.

I thought I would put this post out there, primarily to see if anyone savvier than I can shed some light on what the APPLMGMT_PASSWORD store is used for, and specifically what the "secret key" is used for, and what level of importance it has. I've also put this information in to the vsphere channel in the vExpert slack, where there are many very smart folk. I might also try and feed this information back to VMware as it may or may not be beneficial for the support team.