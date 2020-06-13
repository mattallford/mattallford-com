---
author: Matt Allford
date: 2016-09-12 22:07:08+00:00
draft: false
title: Repoint vCenter Server to PSC
type: post
url: /2016/repoint-vcenter-server-to-psc/
categories:
- '2016'
- September
tags:
- cmsso-util
- PSC
- repoint
- vc
- vcenter
- VCSA
- VMware
---

# Introduction


This article will walk through the steps required to repoint a vCenter Server 6U1 or later node to a different Platform Services Controller (PSC) node. Scenario one will walk through the steps if the destination PSC is located within the same vSphere SSO site, where as scenario two will walk through the steps if the destination PSC is located in a different vSphere SSO site. This is almost the same process, but repointing a VC to a PSC in a different SSO Site than the original PSC requires an additional step.

It is worth noting that as of vSphere 6.0U2, the limitation for repointing a VC node to another PSC is still within the same vSphere SSO domain.

**Edit 21/11/2016:** Please note that in vSphere 6.5 the ability to repoint a VC server to a PSC in another vSphere SSO site is not supported. See [this](http://virtualtassie.com/2016/vcenter-6-5-psc-repoint-limitations/) post for details

These steps will not assist with converting an embedded deployment (VC and PSC roles located on the same node) to an external deployment. This is performed by doing a reconfiguration (rather than a repoint). Please see this article for a high level introduction, which also includes a link to another article I have written which contains the steps on how to perform a reconfiguration if this is what your requirements are.

---
**NOTE**

The steps outlined in this article is specifically for the vCenter Server Appliance (VCSA). VCSA is the preferred deployment model by VMware and as announced at VMworld 2016, it looks like enhancements will come to the VCSA that will not come to the traditional Windows deployment, so the VCSA is what I run in the lab unless I specifically need a windows installation for a task.

---

> :warning: These steps will stop and start the vCenter Server services, so ensure you are doing this within a maintenance window.



# Repoint VC to PSC Located In the Same SSO Site


While you can use **vmafd-cli** as per KB2113917, cmsso-util automates these steps, so I'm going to be using cmsso-util below.

1. I recommend taking snapshots of the vCenter server you are about to repoint, as well as all PSC nodes within the domain
2. This may be obvious, but ensure you have deployed an external PSC in the same vSphere SSO domain site that will be the node you repoint the vCenter Server to

In my scenario, I have VC01 which is running vCenter, PSC01 which is the PSC node the vCenter server is currently pointed to, and PSC02 which is the PSC node the vCenter server will be repointed to during the process.


## VCSA


SSH to the vCenter server and change to the BASH shell by entering the following:

```bash
shell.set --enabled True
shell
```

![Repoint_SameSite_4](/wp-content/uploads/2016/09/Repoint_SameSite_4-1024x558.png)


**Optional.** Run the following command from an SSH session on the vCenter server / PSC to confirm the SSO site name


```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-site-name --server-name localhost
```


[![Repoint_SameSite_1](/wp-content/uploads/2016/09/Repoint_SameSite_1-1024x55.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_1.png)

**Optional.** Run the following command from an SSH session on the vCenter server / PSC to confirm the SSO domain name (vsphere.local by default)


```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-domain-name --server-name localhost
```


[![Repoint_SameSite_2](/wp-content/uploads/2016/09/Repoint_SameSite_2-1024x50.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_2.png)

**Optional.** Run the following command from an SSH session on the vCenter server. This shows the PSC node the vCenter server is currently pointed to

```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-ls-location --server-name localhost
```

[![Repoint_SameSite_3](/wp-content/uploads/2016/09/Repoint_SameSite_3-1024x54.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_3.png)

It is now time to run cmsso-util to perform the repoint. The command is simply:

```bash
cmsso-util repoint --repoint-psc "PSCNAME"
```

So in my environment, I'm going to run the following:

```bash
cmsso-util repoint --repoint-psc "psc02.lab.allford.id.au"
```

After a few minutes, hopefully you will see the message below, indicating that the repoint has been successful

[![Repoint_SameSite_5](/wp-content/uploads/2016/09/Repoint_SameSite_5-1024x288.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_5.png)

We can use vmafd-cli again on the vCenter server to confirm the VC is now repointed to the second PSC node

[![Repoint_SameSite_6](/wp-content/uploads/2016/09/Repoint_SameSite_6-1024x84.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_6.png)

The repoint is now complete!


# Repoint VC to PSC Located In a Different SSO Site

1. This may be obvious, but ensure you have deployed an external PSC in the same vSphere SSO domain but in a different SSO site that will be the node you repoint the vCenter Server to
2. Repointing vCenter to a PSC in a different SSO site is similar to doing the same action within the same SSO site, with two differences:
    1. You need to download a different version of cmsso-util from the following VMware KB article - [KB 2131191](https://kb.vmware.com/selfservice/search.do?cmd=displayKC&docType=kc&docTypeID=DT_KB_1_1&externalId=2131191). I'm not sure why this copy of cmsso-util isn't shipped with the appliances
    2. We need to run an additional command after the repoint has been actioned to move the vCenter services



In my scenario, I have VC01 which is running vCenter, PSC01 which is the PSC node the vCenter server is currently pointed to, located in the site 'site1' and PSC02 which is the PSC node the vCenter server will be repointed to during the process, located in the site 'site2'.


## VCSA


SSH to the vCenter server and change to the BASH shell by entering the following:

```bash
shell.set --enabled True
shell
```

![Repoint_SameSite_4](/wp-content/uploads/2016/09/Repoint_SameSite_4-1024x558.png)


**Optional.** Run the following command from an SSH session on the vCenter server / PSC to confirm the SSO site name. For this action, the SSO site name of the vCenter server and the destination PSC should be different. As you can see below, my VC is in site1 and the PSC is in site2

```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-site-name --server-name localhost
```

[![Repoint_SameSite_1](/wp-content/uploads/2016/09/Repoint_SameSite_1-1024x55.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_1.png)

[![Repoint_DifferentSite_1](/wp-content/uploads/2016/09/Repoint_DifferentSite_1-1024x54.png)
](/wp-content/uploads/2016/09/Repoint_DifferentSite_1.png)

**Optional.** Run the following command from an SSH session on the vCenter server / PSC to confirm the SSO domain name (vsphere.local by default)

```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-domain-name --server-name localhost
```

[![Repoint_SameSite_2](/wp-content/uploads/2016/09/Repoint_SameSite_2-1024x50.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_2.png)

**Optional.** Run the following command from an SSH session on the vCenter server. This shows the PSC node the vCenter server is currently pointed to

```bash
/usr/lib/vmware-vmafd/bin/vmafd-cli get-ls-location --server-name localhost
```

[![Repoint_SameSite_3](/wp-content/uploads/2016/09/Repoint_SameSite_3-1024x54.png)
](/wp-content/uploads/2016/09/Repoint_SameSite_3.png)

Browse to [KB 2131191](https://kb.vmware.com/selfservice/search.do?cmd=displayKC&docType=kc&docTypeID=DT_KB_1_1&externalId=2131191), download the ZIP attachment and extract the file (inside is a single file)



Backup the current cmsso-util file on the vCenter server

```bash
mv /bin/cmsso-util /bin/cmsso-util.bak
```


We now need to copy the newly downloaded cmsso-util file into the /bin folder on the vCenter server. I use WinSCP. You may need to reference [KB 2107727](https://kb.vmware.com/selfservice/search.do?cmd=displayKC&docType=kc&docTypeID=DT_KB_1_1&externalId=2107727) to allow WinSCP connections to the appliance

After the file has been uploaded, run the following to make the file executable

```bash
chmod +x /bin/cmsso-util
```

It is now time to run cmsso-util to perform the repoint. The command is simply:

```bash
cmsso-util repoint --repoint-psc PSCNAME
```

So in my environment, I'm going to run the following:

```bash
cmsso-util repoint --repoint-psc psc02.lab.allford.id.au
```

After a few minutes, hopefully you will see the message below, indicating that the repoint has been successful

[![Repoint_DifferentSite_2](/wp-content/uploads/2016/09/Repoint_DifferentSite_2-1024x284.png)
](/wp-content/uploads/2016/09/Repoint_DifferentSite_2.png)

We can use vmafd-cli again on the vCenter server to confirm the VC is now repointed to the second PSC node

[![Repoint_DifferentSite_3](/wp-content/uploads/2016/09/Repoint_DifferentSite_3-1024x71.png)
](/wp-content/uploads/2016/09/Repoint_DifferentSite_3.png)

From KB 2113115 - A Site in the VMware Directory Service is a logical container in which we group the Platform Services Controllers' server objects within a vSphere Domain. You can name them in an intuitive way for easier implementation. Additionally, when Platform Services Controllers are deployed, they publish their service information (service registrations) into the defined Site. When vCenter Servers are deployed against the Platform Services Controllers, the vCenter Server will publish its service information into the Site in which the Platform Services Controller belongs. If you need to move vCenter Servers between Site, you must move their respective service information.

You can either run the script without any configuration options defined, and you will get prompted:

```bash
cmsso-util move-services
```

Or alternatively you can set the configuration options as per below, which is specific to my environment

```bash
cmsso-util move-services --psc-node psc02.lab.allford.id.au --domain-name vsphere.local --username Administrator --passwd password --oldsite-name site1 --newsite-name site2
```

The repoint is now complete!
