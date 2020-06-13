---
author: Matt Allford
date: 2017-01-12 11:48:58+00:00
draft: false
title: vSphere 6.5 - GUI VCSA Embedded Deployment Walkthrough
type: post
url: /2017/vsphere-6-5-gui-vcsa-embedded-deployment-walkthrough/
categories:
- '2017'
- January
tags:
- appliance
- embedded
- VCSA
- VMware
- vsphere
- vsphere 6.5
- vsphere65
---

The post below will walk through the deployment of an embedded vCenter Server Appliance 6.5 using the GUI installer, where the PSC and VC roles are installed on the same servers. This deployment model is perfect for smaller environment where Enhanced Linked Mode is not required.

The [PSC Topology Decision Tree](https://blogs.vmware.com/vsphere/2016/04/platform-services-controller-topology-decision-tree.html) is a great read if you aren't sure on how you should deploy these core components given your requirements.

If you are interested in also seeing other GUI/CLI deployments, please see my post [here](http://virtualtassie.com/2017/vsphere-6-5-vcsa-deployment-walkthroughs/) which has links off to other individual articles for different deployment methods.

For each of the deployment methods I am providing a video as well. Please find the video for the VCSA Embedded GUI deployment below. This has been recorded in FullHD, so best to watch it in Full Screen!

{{< youtube tZ4jIn39MWI >}}

The deployment of the VCSA is now broken into two stages, which can be completed one after another if desired. Stage one walks through the appliance deployment options, where as stage two walks through specific configuration details of the appliance.

I'm doing the deployment from a Windows machine, but with vSphere 6.5 you of course have the choice of running the installer from Windows, Linux or OSX.

Let's dive into the installation.


# First Steps


The first thing I do before deploying any appliance is making sure there is a DNS record with forward and reverse records. For this walkthrough I'm naming the appliance testvc01.lab.allford.id.au (192.168.0.150).

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_DNS.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_DNS.png)


# Exploring the VCSA ISO


Next, let's take a look at the VCSA ISO file that I've downloaded from VMware. If we mount the ISO file and then open it, you'll see a folder named **vcsa-ui-installer**. Inside there, you'll find folders for **Linux, Mac** and **Windows.** Inside the relevant folder for the operating system you are deploying from, there will be an installer file. For windows, this is **installer.exe**. Double click to launch this

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_1.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_1.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_2.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_2.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_3.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Folder_3.png)


# Appliance Deployment

## Stage 1

As mentioned, stage 1 handles the deployment of the appliance. After launching the installer, you will be presented with a number of options. Click Install.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Installer-1024x739.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Installer.png)

Click Next past the introduction.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Intro.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Intro.png)

Accept the End User License Agreement and click Next.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_EULA.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_EULA.png)

Select vCenter Server with an Embedded Platform Services Controller to select the embedded deployment and click Next

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Deployment_Model.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Deployment_Model.png)

The next step is to enter in the details for the target node of where the appliance will be deployed to. I'm deploying the appliance to an ESXi Server, you do also have the option to deploy it to another vCenter Server.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Target_Node.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Target_Node.png)

If the target node does not have a valid SSL certificate trusted by the machine you are doing the deployment from you will see the following warning. Ensure you have selected to the correct destination node and click Yes.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Cert.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Cert.png)

The next step is to enter in the name of the VCSA we are deploying. This is the name of the appliance as it will appear in the target ESXi / vCenter node, not the actual system name itself. Also enter in the password for the root account on the appliance and click next

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_App_Name.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_App_Name.png)

We now need to pick a deployment size. We are shown a table here of the different deployment sizes, the required resources and also the suggested hosts / VMs to be supported by that appliance size. You can pick the relevant appliance size here based on your requirements. I'm going to choose Tiny for the lab.

There is also an option to change the storage size. This can be handy if you are deploying a smaller size appliance but you want to keep a lot of tasks, events, stats, etc. I'm going to leave mine at default and click Next.

[![](/wp-content/uploads/2017/01/VCSA_Size.png)
](/wp-content/uploads/2017/01/VCSA_Size.png)

We're now shown the available datastores on the target ESXi node / vCenter Server to place the new appliance on to. Select a relevant datastore and choose whether you want to enable thin disk mode for the appliance or not and click Next.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Target_Datastore.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Target_Datastore.png)

On this screen, there are a number of settings to enter

* **Network:** Select the port group that the newly deployed VCSA should be allocated to. This will be populated with networks on the ESXi host or vCenter Server you chose to deploy to in step 4
* **IP Version**: IPv4/IPv6
* **IP Assignment**: Static / DHCP
* **System Name**: I enter the FQDN of the node here. This will be used for the PNID moving forward which cannot be changes. If this field is left empty, the IP address of the node will be used as the system name
* **IP Address**: Enter the IP address for the VCSA
* **Subnet Mask or prefix length**: Enter the subnet mask for the VCSA
* **Default Gateway**: Enter the default gateway for the VCSA
* **DNS Servers**: Enter the DNS server(s) that the VCSA should be used, comma separated if there are more than one

[![](/wp-content/uploads/2017/01/VCSA_Network.png)
](/wp-content/uploads/2017/01/VCSA_Network.png)

We're now shown a summary of the settings that we've chosen throughout the wizard. Confirm the settings and click Finish to begin the deployment. The deployment process takes around 10-15 minutes, and hopefully you see the last screen below showing a successful deployment.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Summary.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Summary.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Deploy-1024x737.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Deploy.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Deploy2.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage1_Deploy2.png)


## Stage 2

Now that stage one is complete, it is time to proceed to step 2 which is the configuration of the appliance. Step 2 can be started by clicking continue in the screen above, or alternatively after stage one is complete you can browse to the VAMI interface of the server (https://fqdn:5480) to complete stage 2.

Click Next at the introduction screen

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_intro.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_intro.png)

This step is to configure time sync for the appliance which can be either by syncing with the ESXi host or by entering details for NTP servers. I've chosen sync with a time server. Also choose whether you want SSH enabled on the appliance or not and click Next.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_NTP.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_NTP.png)

On the SSO Configuration screen, enter the details for a new SSO domain and administrator account as this is an embedded deployment so it cannot join an existing SSO domain.

* **SSO domain name**: In here you can choose whatever you like, but I've never strayed from the default vsphere.local. Make sure this doesn't conflict with any other directories such as Active Directory or LDAP
* **SSO password**: Enter a strong password for the SSO administrator account
* **Site Name**: vSphere SSO domains contain sites. These historically haven't been used much, but my current advice would be to use sites to match physical locations. vSphere SSO sites are familiar to sites in Active Directory

After entering the required information, click **Next.**

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_SSO.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_SSO.png)

Choose whether to join the CEIP or not and click Next.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_CIEP.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_CIEP.png)

We're now presented with a summary screen for stage 2. Confirm all of the settings, especially NTP and DNS and then click Finish.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Summary.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Summary.png)

You'll receive a warning that there is no going back after you click OK.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Warning.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Warning.png)

The configuration of the appliance will now start and similarly to stage 1 you'll be provided with a progress window. With any luck, you'll see the screen below showing a successful setup of the appliance is complete, and you'll also be provided with some URLs to the getting started page and the vSphere Web Client.

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Complete.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Stage2_Complete.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_1.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_1.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_2.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_2.png)

[![](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_3-1024x686.png)
](/wp-content/uploads/2017/01/VCSA_GUI_Embedded_Browser_3.png)


# Summary
We've now got an operation VCSA embedded node that has been deployed using the new GUI installer!

As mentioned in the intro, please check out the video as well as the links off to other deployment methods that may be of interest.
