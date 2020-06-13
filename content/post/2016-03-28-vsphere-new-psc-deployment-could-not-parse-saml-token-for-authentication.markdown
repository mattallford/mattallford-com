---
author: Matt Allford
date: 2016-03-28 11:56:37+00:00
draft: false
title: vSphere New PSC Deployment - Could Not Parse SAML Token For Authentication
type: post
url: /2016/march/vsphere-new-psc-deployment-could-not-parse-saml-token-for-authentication/
categories:
- March
tags:
- external
- PSC
- repoint
- SAML
- time
- vcenter
- VMware
- vsphere
---

In my lab, I deployed vCenter using the appliance as an embedded node using vCenter 6.0.0b. I purposely deployed this build, as an embedded deployment for two reasons:

1. I haven't used the appliance before, and I wanted the lab to be simple, but I also wanted to see the upgrade process in action on the appliance
2. I wanted to deploy an external PSC and follow the process to re point the original VC server to the new external PSC, as outlined here: http://pubs.vmware.com/vsphere-60/index.jsp?topic=%2Fcom.vmware.vsphere.upgrade.doc%2FGUID-E7DFB362-1875-4BCF-AB84-4F21408F87A6.html

My lab is deployed in a nested environment sitting on top of VMWare Workstation. So I deployed a new node to the environment which was an external PSC, joined to the original SSO domain and site.

After the deployment, which was successful, and before going through the repointing process, I just wanted to confirm that the node could be seen from the vCenter web client and was healthy.

From the vCenter Web Client, I went to **System Configuration** > **Nodes**. The new PSC node was listed here, which was good, but after I clicked on the node I received an error:

The appliance management service on PSC01.lab.allford.id.au is not running

[![Appliance_Service_error](/wp-content/uploads/2016/03/Appliance_Service_error-300x159.png)
](/wp-content/uploads/2016/03/Appliance_Service_error.png)

I performed a quick check of the PSC node services and gave it a restart, but no luck. After the restart, when I came back to this screen and received a second error, which pointed me in the right direction, and was something simple that I should have checked during the deployment:

Could not parse SAML token for authenticaion

[![SAML Token](/wp-content/uploads/2016/03/SAML-Token-300x66.png)
](/wp-content/uploads/2016/03/SAML-Token.png)

As soon as I saw this, I realised I did not check the time configuration on the nodes. A quick check of the date on the existing embedded deployment and also on the new PSC showed a large difference, meaning that indeed the SAML token I received after logging into vCenter was not valid when querying the new PSC node.

[![Node_Time_Check](/wp-content/uploads/2016/03/Node_Time_Check-300x38.png)
](/wp-content/uploads/2016/03/Node_Time_Check.png)

I followed VMWare KB [2113610](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2113610) to set the NTP configuration on both nodes and restarted the services. Note that the time configuration can also be set from the Appliance Management User Interface (A MUI - formerly VAMI) at https://nodename:5480

Time configuration now looked good on both nodes, and checking the new PSC node in the **System Configuration** section of vCenter was now successful.

Unfortunately I spent a few extra minutes troubleshooting something basic that I should have checked and overlooked in the lab. Pulled directly from the 'requirements' section of the vSphere 6 deployment guide:

_Time – Ensure that time is synchronized across the environment_


