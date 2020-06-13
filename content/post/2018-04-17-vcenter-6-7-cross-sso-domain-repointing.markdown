---
author: Matt Allford
date: 2018-04-17 14:10:18+00:00
draft: false
title: vCenter 6.7 Cross SSO Domain Repointing
type: post
url: /2018/vcenter-6-7-cross-sso-domain-repointing/
categories:
- '2018'
- April
tags:
- '6.7'
- repoint
- SSO
- sso domain
- vcenter
- VMware
- vsphere
- vsphere 6.7
---

It's back, finally! A new feature with vCenter 6.7 is the ability to repoint a vCenter Server to another Platform Services Controller node, that resides in an **entirely different vSphere SSO domain**. This functionality is huge for domain consolidation, and also domain splitting (which admittedly is a less required use case from what I've seen, but something that still can be a useful use case).

Edit: As per a comment from Rupak, I believe this feature is only available on the vCenter Server Appliance and is not available for the Windows deployment of vCenter 6.7. With that said, hopefully if you are running vCenter 6.7 you are now running the appliance, as you have likely had several chances to migrate to the VCSA either using the migration tool, or manually (albeit with plenty of scripts to assist with the move of config and workloads).

I say _new,_ but really I should say _returned,_ as this functionality was available to vSphere administrators in vSphere 5.5, and has been a pain point of versions 6.0 and 6.5 of the product as it constrained the ability to consolidate or split without building new vCenter Servers, based on business requirements or consolidation of IT resources within an organisation.

After a cross-domain repoint, services such as tagging and licensing are migrated to the new Platform Services Controller.

In the post below, I'm going to take you through the process of doing a Cross SSO Domain Repoint of vCenter Server 6.7.


# Scenario


I'm going to use a straight forward example here, where we have two fictional companies; Company A and Company B. Yeah, original aye! We have two SSO domains (companya.local and companyb.local), and each SSO domain has one PSC node and one vCenter Server.

Company A has acquired Company B, and the IT team has decided to consolidate the deployment, and wish to repoint Company B's vCenter Server (companyb-vc01) to their own SSO domain (companya.local) and PSC node (companya-psc01).

Here's a basic diagram of the environment prior to any changes:

[![](/wp-content/uploads/2018/01/6.7_SSORepoint_Environment_Before.png)
](/wp-content/uploads/2018/01/6.7_SSORepoint_Environment_Before.png)

And he's the desired end state we are trying to achieve. I've still put Company B's PSC node in the diagram, as this would still exist after the repoint and would need to be decommissioned manually to complete the process, if that's the desired outcome:

[![](/wp-content/uploads/2018/01/6.7_SSORepoint_Environment_After.png)
](/wp-content/uploads/2018/01/6.7_SSORepoint_Environment_After.png)


# Prerequisites

* Cross domain repointing is only supported with Platform Services Controller 6.7 and vCenter Server 6.7
* Each vCenter Server and vCenter Server node must be in a healthy state
* To ensure no loss of data, take a snapshot and/or backup each node before proceeding with repointing the vCenter Server or Platform Services Controller
* If vCenter High Availability is in use, this should be disabled prior to the repointing, and can be re-enabled after a successful repoint
* The repointing supports external deployment models only. You _**can**_ repoint an embedded vCenter Server to a PSC in another SSO domain, but you first need to externalise the embedded deployment



# Pre-check and Conflicts

So by now you might be thinking about conflicts. What if there are objects in the destination environment that are the same as the source environment? Thankfully VMware have thought about this and they provide the option to run a pre-check for conflicts.

The pre-check mode fetches the tagging (tags and categories) and authorisation (roles and privileges) data from the Platform Services Controller. Conflicts can be checked for tagging and authorisation data. The Pre-check does not migrate any data, but checks the conflicts and writes them to a JSON file.

The JSON file can then be reviewed to see if there are any conflicts and if so, an administrator can provide detailed input on how to handle various conflicts.

I'm planning to do another post on this section, or edit this section of this post with more information on how to handle the conflicts, but in summary there are 3 actions you can pick for the conflicts. The examples below are specifically for roles, but these actions are valid for all conflict types:

**COPY.** A copy of the conflicting role is created in the target Platform Services Controller, with –copy appended to the role name. The new role is created with a new role ID with the same set of privilege IDs. The new role ID is updated in the VPX_ACCESS table. The new role ID is applicable for both role name conflict and role ID conflict.

**MERGE.** The MERGE option is resolved in the following sequence:

1. If the source Platform Services Controller has a role with the same name and privilege list as a role in the target Platform Services Controller, but the role IDs are different, the role ID from the target Platform Services Controller is used and updated in the VPX_ACCESS table.
2. If the source Platform Services Controller has a role with the same name as a role in the target Platform Services Controller, but with a different privilege list, then the privilege lists for both roles are merged

**SKIP.** Do nothing. The specific role is skipped.


# Process

To perform the repoint, we can use the **cmsso-util** command with the **domain-repoint** option. The mandatory arguments are listed below:

`--mode`
Mode can be pre-check or execute.

`--src-psc-admin`

This is the SSO administrator user name for the source Platform Services Controller. Do not append the @domain, just state the username.

`--dest-psc-fqdn`

The FQDN of the Platform Services Controller to repoint the vCenter Server to.

`--dest-psc-admin`

This is the SSO administrator user name for the destination Platform Services Controller. Do not append the @domain, just state the username.

`--dest-domain-name`

SSO domain name of the destination Platform Services Controller.

`--dest-vc-fqdn`

The FQDN of the vCenter Server pointing to a destination Platform Services Controller. The vCenter Server is used to check for component data conflicts in the pre-check mode. If not provided, conflict checks are skipped and the default resolution (COPY) is applied for any conflicts found during the import process. This argument is optional only if the destination domain does not have a vCenter Server. If a vCenter Server exists in the destination domain, this argument is mandatory.

So, coming back to our example, I'm going to log in to the vCenter Server companyb-vc01.lab.virtualtassie.com and run the following command:

```
cmsso-util domain-repoint --mode execute --src-psc-admin administrator --dest-psc-fqdn companya-psc01.lab.virtualtassie.com --dest-psc-admin administrator --dest-domain-name companya.local --dest-vc-fqdn companya-vc01.lab.virtualtassie.com
```

We get prompted for the source and target PSC administrator passwords, and then get the following warning and information screen. Please take a few minutes to actually read the warning, it's important.

There's also a prompt asking if the settings are correct and do we want to continue:

[![](/wp-content/uploads/2018/04/6.7_SSORepointCommand1.png)
](/wp-content/uploads/2018/04/6.7_SSORepointCommand1.png)


After proceeding with the repoint, the tool will go through various steps to export and import the data / services in to the target SSO domain and repoint the vCenter Server. We're given a breakdown of each stage with a status of Done or Failed (I believe). In the lab on a brand new deployment, the repoint took around 15 minutes. Obviously YMMV and this is might be longer in a production environment with actual data within the environment.

[![](/wp-content/uploads/2018/04/6.7_SSORepointCommand2.png)
](/wp-content/uploads/2018/04/6.7_SSORepointCommand2.png)



Things are looking good and the repoint was successful. If I go ahead and log in to the vSphere Client on either Companya-vc01 or companyb-vc01, I can see that these two vCenter Servers are now in Enhanced Linked Mode with each other, and when looking at the Deployment > System Configuration section of the Web Client, we can see there are now 3 nodes in the topology - 2 vCenter Servers and one Platform Services Controller as per the diagram further up in the blog post.

[![](/wp-content/uploads/2018/04/6.7_SSORepoint_WebClient1.png)
](/wp-content/uploads/2018/04/6.7_SSORepoint_WebClient1.png)



[![](/wp-content/uploads/2018/04/6.7_SSORepoint_WebClient2.png)
](/wp-content/uploads/2018/04/6.7_SSORepoint_WebClient2.png)

Companyb-psc01 still exists, but with nothing else in that SSO domain we can go ahead and safely decommission and delete the virtual appliance.


# Summary
So there you have it. The process for cross domain repointing in 6.7 is quite straight forward and in my (rather basic) testing, has worked well. I'm keen to look at some more complex scenarious with various conflicts in the lab, and I'll be sure to report back any findings.

What do you think of this new functionality? Will you be using it in vSphere 6.7?


