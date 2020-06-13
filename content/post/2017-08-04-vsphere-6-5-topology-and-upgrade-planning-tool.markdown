---
author: Matt Allford
date: 2017-08-04 12:15:21+00:00
draft: false
title: vSphere 6.5 Topology and Upgrade Planning Tool
type: post
url: /2017/vsphere-6-5-topology-and-upgrade-planning-tool/
categories:
- '2017'
- August
tags:
- topology
- upgrade
- VMware
- vsphere
---

Overnight VMware announced a new technical resource for vSphere administrators called vSphere Central. Check out the blog post [here](https://blogs.vmware.com/vsphere/2017/08/welcome-to-vsphere-central.html).

In conjuntion with the above, [Adam Eckerle](https://twitter.com/eck79) released a blog post to announce the availability of a new tool named **vSphere 6.5 Topology and Upgrade Planning Tool**. The blog post can be found [here](https://blogs.vmware.com/vsphere/2017/08/announcing-vsphere-6-5-topology-upgrade-planning-tool.html).

I don't want to repeat much of the post here, but in short to quote Adam; "This tool aims to help customers plan and execute both upgrades to vSphere 6.5 as well as new deployments."

I've just spent a few minutes with the tool, and I have a perfect scenario at the moment to use this tool for, where I've put out a SoW to take a customer from a vSphere 5.5 environment to 6.5. I've put a few thoughts below.


# Using the tool


So I wanted to just jump straight in and see how interactive and intuitive the tool is and overall I was impressed.

I started the tool and was asked questions such as:

* Am I upgrading or deploying new
* What version am I upgrading from
* What topology do I currently have
* What is my desired destination deployment topology
* Do I want to use vCenter HA or load balance PSC nodes

I was then presented with a graphical representation of the recommended topology, as pictured below.

[![](/wp-content/uploads/2017/08/PlanningTool_1.png)
](/wp-content/uploads/2017/08/PlanningTool_1.png)

Cool, looks good. I then hit continue and were faced with another series of questions, such as:

* Do I need to consolidate the SSO domain(s) prior to upgrade?
* Am I running Windows or VCSA in the source environment?

I was then shown the graphic below about the migration tool to get from Windows to VCSA, as well as doing the upgrade at the same time.

[![](/wp-content/uploads/2017/08/PlanningTool_2.png)
](/wp-content/uploads/2017/08/PlanningTool_2.png)

Awesome. I then clicked on finish and was presented with a fantastic page titled "Recommended Upgrade Steps to vCenter Server Appliance 6.5". This is where everything was tied together on a nice clean page with a huge amount of resources. The information here includes:

* A summary of the decisions I made to get to this recommendation
* Steps I need to follow, including a graphic diagram, to get from where I am to where I want to be
* Resources for:
    * Pre Upgrade
    * Upgrade
    * Post Upgrade



The resources includes links to VMware blog posts, the documentation pages and various KB articles.

To top it off, in the top right corner is a button to email the information to yourself. I did this and sure enough, received an email with the information displayed on the recommendations page. A great resource to keep without needing to run through the wizard again.

[![](/wp-content/uploads/2017/08/PlanningTool_3.png)
](/wp-content/uploads/2017/08/PlanningTool_3.png) [![](/wp-content/uploads/2017/08/PlanningTool_4.png)
](/wp-content/uploads/2017/08/PlanningTool_4.png)




# My Thoughts


It's good to see the new documentation being released by VMware. It's intuitive and informational with images and at times, videos, and for me it is much more of a pleasure to read (not sure I'd ever say that about documentation!). This tool is a good start to what I'm sure will get additional scenarios added and over time, involve other VMware products as well. It would certainly be a step up on the table in [KB 2109760](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2109760)!

When I first read about the tool, my initial thoughts were actually about the [VMware vSphere Compatibility Predictor fling](https://labs.vmware.com/flings/future-vsphere-compatibility-predictor#summary), which I wrote about here.

I think it would be great if, in the future, a hybrid of the Fling and the planning topology tool was included in the vCenter Server media files, and you could run it interactively within an environment. The tool could connect into PSC node(s) to automatically detect existing topologies (rather than relying on me to correctly enter it), and other VMware components registered to SSO, and then provide you with the same wizard to plan your upgrade. The recommendations summary could then also include actual names of servers, just to customise the recommendations that little bit more and make injecting the information into a change request that little bit easier.

It would be good to have the tool plan out the upgrade process for the entire vSphere environment, call out any compatibility issues and maybe even flag what other third party tools are registered with SSO. This would be a great tool to have in the kit especially if going into an unknown environment, such as a new customer, and doing a discovery of the environment and getting a quick overview of any upgrade restrictions or considerations.

Have you used the tool yet? If not, go ahead and give it a try. Provide some feedback. Have a think about how this could evolve to make your life easier.

Personally I'm looking forward to seeing this tool evolve and am glad to see the quality and delivery of information from VMware mature.
