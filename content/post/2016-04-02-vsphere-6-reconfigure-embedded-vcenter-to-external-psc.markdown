---
author: Matt Allford
date: 2016-04-02 10:45:08+00:00
draft: false
title: vSphere 6 - Reconfigure Embedded vCenter to External PSC
type: post
url: /2016/vsphere-6-reconfigure-embedded-vcenter-to-external-psc/
categories:
- '2016'
- April
tags:
- Certificate
- cmsso-util
- esxi
- external
- PSC
- repoint
- VCSA
- vmca
- vsphere
---

# Introduction


As of vSphere 6.0U1, VMware allow an embedded vCenter server deployment to be reconfigured to an external deployment, which demotes the Platform Services Controller (PSC) components of the embedded node and points the VC server to an external PSC node which resides in the same Single Sign On (SSO) domain as the source embedded node.

This is done by using the utility **cmsso-util**

Before we get too much further, there are two main uses for cmsso-util:

* **Reconfigure**
    * Reconfigure is used to reconfigure an embedded node to an external model, therefore the VC must be an embedded deployment
    * The target PSC node must be a member of the same SSO domain as the source embedded VC
* **Repoint**
    * Repoint is used to change the PSC node that a VC node is configured to point to
    * To use repoint, the VC must be an external deployment already
    * The target PSC node must be a replication member in the same SSO domain as the original PSC. You cannot repoint a VC node to a PSC node in a different SSO domain



The rest of this post will be discussing the **reconfigure** option, as I migrated from an embedded deployment to an external deployment.

If you want to do a **repoint,** please see my post [here](http://virtualtassie.com/2016/repoint-vcenter-server-to-psc/).

If you want a high level overview of each options, please see my post [here](http://virtualtassie.com/2016/reconfiguring-and-repointing-vcenter-server-to-psc/).

The reconfigure process was pretty smooth, but I did come across 2 issues or nuances. Note that my environment is using the vCSA for VC and PSC nodes, so instructions below are for the vCSA and not the windows installation.

I originally deployed by lab with an embedded vSphere 6 deployment using the VCSA, so the vCenter server and PSC were both running on the same node. I did this specifically so I could go through the process of reconfiguring the vCenter server to use an external PSC after I deployed a new external PSC to the environment.

I deployed an external PSC to my lab environment, into the same SSO domain and SSO site as the embedded node. My lab is running VMWare Workstation, so deploying a PSC node using the VCSA is not done through the browser UI as usual, and required a couple of tweaks to the virtual machine config file to make sure it joined the original SSO domain successfully and started PSC replication. I'll probably do a post on this process in a few days as I am planning to deploy another external PSC.

**Update:** [Here ](http://virtualtassie.com/2016/vsphere-6-add-second-external-psc-to-nested-lab-environment/)is a post on deploying an external PSC into an existing environment in VMware Workstation.

To confirm, when using cmsso-util to reconfigure the vCenter server to an external PSC, the process demotes the embedded deployment to just a management (vCenter) node.


# Reconfigure using cmsso-util

* The reconfiguration of a vCenter Server embedded instance to an external PSC is a one-way process
* The VMware documentation for this process is [here](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.vsphere.upgrade.doc/GUID-E7DFB362-1875-4BCF-AB84-4F21408F87A6.html)
* Ensure you have deployed a new external PSC into the same SSO domain as the embedded deployment and confirmed the environment is healthy

Remember these steps before making any changes

1. You can validate the SSO configuration by using vmafd-cli (see the VMware article above)
2. Take snapshots of all PSC nodes in the SSO domain and the vCenter server you are doing the reconfigure on, in case of any issues

**Process:**

1. Log in to the embedded VC node
2. Run cmsso-utilas per below. Change **externalpsc, password** (and **administrator** / **vsphere.local** if you do not use the default)

```bash
cmsso-util reconfigure --repoint-psc externalPSC --username administrator --domain-name vsphere.local --passwd password
```

You should (hopefully!) see a message similar to:


<blockquote>The vCenter Server has been successfully reconfigured and repointed to the external Platform Services Controller PSC01.lab.allford.id.au.</blockquote>


If the command returned a successful result, log in to the vCenter server and confirm vCenter is running and healthy.

At this point, VC server that used to be embedded with the PSC roles has been demoted to a VC only server, and it has been pointed to the external PSC.

Two issues / nuances I faced were PNID case sensitivity when performing the reconfigure, and also a clean up / reconfiguration of SSL certificates may be required. I've written about both of these below.


# PNID Case Sensitivity


When specifying the Primary Network Identifier (PNID) of the new PSC in the cmsso-util command, make sure you get the case correct.

The PNID of the node can be found by running the following:

```bash    
/usr/lib/vmware-vmafd/bin/vmafd-cli get-pnid --server-name localhost
```

I originally ran the following and received an error:

```
**_VC01:~ # cmsso-util reconfigure --repoint-psc psc01.lab.allford.id.au --username administrator --domain-name vsphere.local --passwd password Validating Provided Configuration ..._**
_Error: The provided Platform Services Controller(PSC) psc01.lab.allford.id.au is not a replication partner of the localhost_
_. Please make sure to provide the Primary Network Identifier (PNID) of the PSC._
```


Changing the case of my external PSC in the commands from psc01 to PSC01 resolved this issue:

```
**VC01:~ # cmsso-util reconfigure --repoint-psc PSC01.lab.allford.id.au --username administrator --domain-name vsphere.local --passwd password**
Validating Provided Configuration ...
Validation Completed Successfully.
Executing reconfiguring steps. This will take few minutes to complete.
Please wait ...
Stopping all the services ...
All services stopped.
Starting vmafd service.
Successfully joined the external PSC PSC01.lab.allford.id.au
Cleaning up...
Cleanup completed
Starting all the services ...
Started all the services.
The vCenter Server has been successfully reconfigured and repointed to the external Platform Services Controller PSC01.lab.allford.id.au.
```


Logging into the vCenter server with SSH now shows it as a vCenter server with an external PSC

[![VC_After_Repoint](/wp-content/uploads/2016/04/VC_After_Repoint.png)
](/wp-content/uploads/2016/04/VC_After_Repoint.png)


# Certificates


So in my lab, I'm just using the VMCA for certificates. Keeping it simple until I have a need to do anything additional like custom certs or setting up the VMCA as a subordinate. When I originally deployed the environment as embedded, the certificates for my ESXi servers and the vCenter server were issued by the now non-existent PSC (as I am now running a single external PSC).

Just to confirm there were no remnants left behind, I connected to my vSphere.local domain with JXplorer and checked to see if my new external PSC was the only PSC in the domain, and it is. If there were others, I would see other entries under 'servers', and also there would be replication agreements between the PSC nodes.

[![JXPlorer_PSC](/wp-content/uploads/2016/04/JXPlorer_PSC.png)
](/wp-content/uploads/2016/04/JXPlorer_PSC.png)

So, I now wanted to do a clean up of the environment and re-issue certificates for the vCenter server and for the ESXi hosts from the VMCA running on the newly deployed external PSC.


## vCenter Certificates


As you may know, certificates can be managed using the Certificate-Manager utility found at **/usr/lib/vmware-vmca/bin/certificate-manager** on the appliance.

I started with running the certificate-manager utility on the vCenter server and wanted to replace the machine SSL certificate with a VMCA certificate (option 3). After entering in the administrator credentials and the external PSC details, the process failed:

```
Error: 382312514, VMCAGetSignedCertificatePrivate() failedStatus : Failed
Error Code : 382312514
Error Message : Failed to connect to the remote host, reason = rpc_s_connect_rejected (0x16c9a042).
Status : 0% Completed [Operation failed, performing automatic rollback]
```

As advised in the SSH window, I checked the following log file **/var/log/vmware/vmcad/certificate-manager.log** and found the following

```
2016-03-28T02:13:44.980Z INFO certificate-manager Generating cert...
2016-03-28T02:13:44.980Z INFO certificate-manager Running command :- ['/usr/lib/vmware-vmca/bin/certool', '--server=localhost', '--gencert', '--privkey=/storage/certmanager/MACHINE_SSL_CERT.priv', '--cert=/storage/certmanager/MACHINE_SSL_CERT.crt', '--config=/var/tmp/vmware/MACHINE_SSL_CERT.cfg']
2016-03-28T02:13:45.16Z INFO certificate-manager Command output :-
Using config file : /var/tmp/vmware/MACHINE_SSL_CERT.cfg
Error: 382312514, VMCAGetSignedCertificatePrivate() failedStatus : Failed
Error Code : 382312514
Error Message : Failed to connect to the remote host, reason = rpc_s_connect_rejected (0x16c9a042).
2016-03-28T02:13:45.17Z ERROR certificate-manager Using config file : /var/tmp/vmware/MACHINE_SSL_CERT.cfg
Error: 382312514, VMCAGetSignedCertificatePrivate() failedStatus : Failed
Error Code : 382312514
Error Message : Failed to connect to the remote host, reason = rpc_s_connect_rejected (0x16c9a042).
```

As there was nothing obvious here, I hit up Google and found the following VMWare KB article - [After migrating the vCenter Server 6.0 from an Embedded Platform Services Controller to External Platform Services Controller, certificate regeneration fails with the error code: 382312514 (2133028)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2133028)

Bingo!

Copied from the 'cause' section of the article;

**This issue is due to the vCenter Server still containing the decommissioned VMCA's Root certificate causing the certificate-manager utility to believe it is still an embedded node.**

Fair enough. The resolution is straight forward as well, and just involves renaming the following file on the vCenter server that used to host the embedded PSC; /var/lib/vmware/vmca/root.cer

After doing the rename, I ran the certificate-manager utility again and it worked with no issues at all.

## ESXi Certificates

ESXi certificate renewal from the VMCA can be done a couple of ways and is pretty straight forward. In the vSphere Web Client, browse to the ESXi host in the hosts and clusters view and go to Manage > Settings > Certificate. As you can see, the certificate on the host has been signed by the vCenter server VC01 which no longer has an embedded PSC.


[![ESXi_Cert](/wp-content/uploads/2016/04/ESXi_Cert.png)
](/wp-content/uploads/2016/04/ESXi_Cert.png)

Clicking on the Renew button, or right clicking on the host > Certificates > Renew Certificate will generate a new certificate to the ESXi host from the new external PSC (PSC01).

[![ESXi_Cert_Post](/wp-content/uploads/2016/04/ESXi_Cert_Post.png)
](/wp-content/uploads/2016/04/ESXi_Cert_Post.png)