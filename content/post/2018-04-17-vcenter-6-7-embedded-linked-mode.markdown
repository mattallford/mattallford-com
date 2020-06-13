---
author: Matt Allford
date: 2018-04-17 14:17:07+00:00
draft: false
title: vCenter 6.7 Embedded Linked Mode
type: post
url: /2018/vcenter-6-7-embedded-linked-mode/
categories:
- '2018'
- April
tags:
- '6.7'
- embedded linked mode
- vcenter
- VCSA
- VMware
- vsphere
- vsphere 6.7
---

[toc]


# Introduction

Starting from VMworld US 2017, VMware announced their plans to support PSC replication and Enhanced Linked Mode with the vCenter Embedded deployment model, simplifying overall deployment and management requirements for a multi-vCenter environment.

This feature is now included in the release of vSphere 6.7. In this post, I go over a little history for common deployment models, as well as what the new "vCenter Embedded Linked Mode" brings to the table. I also compare a couple of topologies between vSphere 6.0/6.5 and vSphere 6.7 to show the savings in the amount of nodes and in some cases, the complexity, that can be saved by using this new deployment topology.

# A Little History


If you are really familiar with vCenter 5.5, 6.0 and 6.5 topologies, it might be worth skipping this section.

With **vSphere 5.5**, we have several roles that make up the vCenter Server "solution", if you will. These are broken down as follows:

* Single Sign On
* vSphere Web Client
* vCenter Inventory Service
* vCenter Server
* vCenter Server Database

The recommended approach for deploying vCenter Server in almost all scenarios, involves a single machine for the vCenter Server components (SSO, vSphere Web Client, vCenter Inventory Service and vCenter Server) and a separate machine for the vCenter Server database. Administrators had the option of splitting the components listed above across multiple machines using the custom install, and there are reasons for doing this, which usually came down to scale and performance in large environments, or simplified management (such as centralising SSO / vSphere Web Client)

In 5.5, there was an a technology named Linked Mode. Enabling Linked Mode places all vCenter Server instances running the same version into a Linked Mode group and provides a centralised management view of all linked vCenter Server instances. It also replicates the roles and permissions between vCenter Server instances. It's fair to say that Linked Mode is easy to set up in 5.5 and is very popular.


Then came **vSphere 6.0 / 6.5**, and the topology was simplified into two main roles:

* Platform Services Controller (PSC)
* vCenter Server
* To compare with 5.5, I'll also list the vCenter Server Database

vSphere 6.0 / 6.5 also introduced "Enhanced Linked Mode". From the [VMware Documentation Site](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.upgrade.doc/GUID-91EF7282-C45A-4E48-ADB0-5A4230A91FF2.html) - 'Enhanced Linked Mode lets you view and search across all linked vCenter Server systems and replicate roles, permissions, licenses, policies, and tags'

There are two models in which vCenter 6.0 / 6.5 can be deployed; **Embedded** or **External.**

An embedded deployment means the PSC and vCenter Server roles are installed on a single server. The embedded deployment model is easy to deploy and maintain, and multiple standalone embedded deployments are supported in a single environment. The down side is that an embedded deployment cannot replicate to another embedded deployment, and Enhanced Linked Mode is not supported.

An external deployment means the PSC is deployed on a single server, and then the vCenter Server is installed on a separate server, which then links to a PSC server. Multiple vCenter Servers can link to a single PSC. External PSC servers replicate with other external PSC servers in the same vSphere SSO domain. External PSC servers can also be load balanced for high availability, albeit at the cost of complexity. By going down the path of an external deployment, Enhanced Linked Mode is automatically enabled, meaning you can manage all vCenter Servers in the vSphere SSO domain from a single vSphere Web Client session. Deciding if you need Enhanced Linked Mode is the primary driver to determine if you should go down the path of an embedded or external deployment model (but do note this isn't the _only_ deciding factor).


# Introducing vCenter Embedded Linked Mode

With the release of vSphere 6.7, the vCenter Server Appliance now has support for an embedded deployment (vCenter Server and PSC on the same node) to be connected with other embedded nodes with what is being named Embedded Linked Mode (yep, still got the ELM acronym!), which allows you to log in and manage all vCenter Servers that are linked within the same SSO domain in a single browser session.

As of this release, VMware are supporting up to 15 embedded nodes to be connected in Embedded Linked Mode. If your environment scales beyond that, then this is a reason to stick with the external model, which is still supported in 6.7.

There is also full support for Embedded Linked Mode when using the vCenter HA and vCSA backup and restore features.

Switching from an external deployment to an embedded deployment can save on the amount of management machines to deploy and manage by up to 75%. Let's take a look at a basic, yet popular deployment with vSphere 6.0/6.5, where there is one vCenter Server with an external PSC in each site. The image below shows two sites as an example:

[![](/wp-content/uploads/2018/01/ELM_SimpleTopology_1-300x214.png)
](/wp-content/uploads/2018/01/ELM_SimpleTopology_1.png)

And if we were to take this example and instead deploy an embedded model, using Embedded Linked Mode, we go from 4 nodes (1 vCenter and 1 PSC in each site, times two sites) down to two nodes (1 embedded PSC/VC in each site, using Embedded Linked Mode), for a machine count saving of 50%:

[![](/wp-content/uploads/2018/01/ELM_SimpleTopology_2-300x159.png)
](/wp-content/uploads/2018/01/ELM_SimpleTopology_2.png)

Now let's take a look at an example of a deployment across two sites, with vCenter High Availability in use within each site. In addition to vCenter High Availability, the Platform Services Controller nodes are also located behind a load balancer for high availability, based on vSphere 6.0/6.5:


[![](/wp-content/uploads/2018/01/ELM_VCHAExample_1.png)
](/wp-content/uploads/2018/01/ELM_VCHAExample_1.png)




If we take the same example above, but simplify the topology using Embedded Linked Mode in vSphere 6.7, we end up with the following architecture. Note that there is no load balancer complexity required, there are less PSC nodes in the environment and only one PSC replication agreement is required:

[![](/wp-content/uploads/2018/01/ELM_VCHAExample_2.png)
](/wp-content/uploads/2018/01/ELM_VCHAExample_2.png)


# Migrating From External To Embedded


Unfortunately at this point in time, there is no single script or tool that can migrate your current external deployment back to an embedded deployment, so for those that have already gone down the path of an external model, getting back to embedded won't be so straight forward.

The only option you really have currently is to spin up a new environment with the embedded model and migrate your configuration, hosts and virtual machines across to the new deployment. You'll need to weigh up if the effort required in going down this path is going to pay off for you and your desired deployment and requirements. Here's hoping VMware are working on a tool to simplify this process!


# Deployment


Ok, so you like the deployment model, how do you actually go about deploying it?

Well, the good news is that if you've deployed vCenter Server with 6.0 or 6.5, the process is really no different. You have the options of the GUI based deployment and my preferred option, the CLI based deployment. When you get around to deploying your second embedded vCenter Server and you want to use Embedded Linked Mode, you simply just specify the first node you deployed as the replication partner.

If you end up deploying more than two nodes, remember that the guidance from VMware is to create a ring replication topology between PSC nodes, and this still applies to this new embedded deployment topology.

If you look at the VCSA CLI deployment JSON templates (located in **\vcsa-cli-installer\templates\install** on the media), we can see two new templates to use for deploying the second or later embedded VCSA replication nodes to either ESXi or vCenter Server. So you would use on of the first two templates for the deployment of your first node, and then one of the second two templates for subsequent nodes, depending on your deployment target (to ESXi to VC).

[![](/wp-content/uploads/2018/02/Media_CLIInstall_Templates.png)
](/wp-content/uploads/2018/02/Media_CLIInstall_Templates.png)

You can refer to some of my earlier walkthrough posts for deploying the vCenter Server Appliance which can be found below, and the process is much the same for 6.7


[vSphere 6.5 – CLI VCSA Embedded Deployment Walkthrough](https://virtualtassie.com/2017/vsphere-6-5-cli-vcsa-embedded-deployment-walkthrough/)




[vSphere 6.5 – CLI VCSA External Deployment Walkthrough](https://virtualtassie.com/2017/vsphere-6-5-cli-vcsa-external-deployment-walkthrough/)




[vSphere 6.5 – GUI VCSA Embedded Deployment Walkthrough](https://virtualtassie.com/2017/vsphere-6-5-gui-vcsa-embedded-deployment-walkthrough/)


I hope this post was helpful to you and you now understand the new Embedded Linked Mode deployment mode that is available in vSphere 6.7.
