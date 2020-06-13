---
author: Matt Allford
date: 2018-11-14 12:03:20+00:00
draft: false
title: vCenter Server 6.7U1 Converge Tool
type: post
url: /2018/vcenter-server-6-7u1-converge-tool/
categories:
- '2018'
- November
tags:
- appliance
- converge
- decommission
- PSC
- vcenter
- VCSA
- VMware
---


# Overview


A few weeks ago VMware released vSphere 6.7U1, and in that release comes a tool that has been desired by many customers and partners, which is the vCenter Converge Tool, or in other words, a tool to convert an external vCenter and PSC architecture in to an embedded deployment.

In the last few releases of vCenter Server, VMware have supported replication of the PSC functionality between embedded deployment models, including Enhanced Linked Mode (named Embedded Linked Mode for an embedded deployment). It has been clear that the embedded model should now be the default model and it is the recommended model from VMware, in fact last week VMware [announced their plans to deprecate the external PSC](https://blogs.vmware.com/vsphere/2018/11/external-platform-services-controller-a-thing-of-the-past.html), but until today we did not have a way to automatically converge an external model in to an embedded model.

Enter **vcsa-util**.

As of vCenter Server 6.7U1, vcsa-util can be used to converge the external PSC's and external VCSA's in to an embedded VCSA deployment. It is important to start thinking about the PSC "role" as being a set of services that have previously been bundles in to a Platform Services Controller name. Things to think about with performing a converge:



* This is only supported for the vCenter Server Appliance. If you are still running Windows vCenter Server, [stop](https://blogs.vmware.com/vsphere/2017/08/farewell-vcenter-server-windows.html)
* Multi OS CLI support - the tool can be run from Windows, Linux and Mac OS
* Supports converging a complex external deployment with a load balancer
* Planning is involved if you have solutions registered to the external PSC that you are going to decommission, such as NSX Manager, Site Recovery Manager, vRealize suite, and so on. Before you decommission the external PSC these services are registered with, you will want to re-register these solutions to the embedded PSC on your target VCSA
* Back up your environment before making any changes. Using the built in VCSA file based backup is a great method
* Remove vCenter High Availability prior to performing the converge if you use VCHA

On the vCenter Server Appliance ISO file, you will see two JSON files in the vcsa-converge-cli directory that are going to be used during the converge and decommission process:

vcsa-converge-cli\templates\converge\converge.json

vcsa-converge-cli\templates\decommission_psc.json

converge.json is filled out prior to the first converge run of installing the PSC RPMs on a vCenter Server.

decommission_psc.json is filled out and used when you are ready to decommission any external PSC nodes in the environment.

# How to use vcsa-util

So, my lab is a great example of a simple topology that I would like to converge. I have two vCenter Servers, in separate SSO sites, each with an external PSC, all in a single vSphere.local domain. The current architecture is pictured below (apparently the icon that looks like a heartbeat is the VVD icon for PSC - keep an eye on it as the PSC services get installed on VCSA nodes).

[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_1.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_1.png)

My desired architecture is the following:

[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_2.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_2.png)

Below is the process I followed to converge my deployment.

1. Upgrade all PSC and VC nodes in the environment to 6.7U1
2. Confirm replication between the existing PSC nodes is healthy by using vdcrepadmin. Take backups of all vCenter Server appliances and at least one PSC node
3. Shut down the vCenter Server nodes and PSC nodes and take a snapshot of the virtual machines (this step is not required, but I wanted to have an easy rollback if required)
4. Delete any vCenter Server Appliance backup schedules if you have these in place, otherwise the [converge will fail](https://virtualtassie.com/2018/vcenter-6-7-update-1-converge-to-embedded-failed/)
5. Check which solutions are registered with external PSC's and plan appropriately. These solutions will need to be re-registered with the embedded PSC services after converging and prior to decommissioning the external PSC nodes. In my environment, this is NSX Manager
6. It is now time to run the converge process against my first vCenter Server; **sitea-vc01.lab.virtualtassie.com**
7. To start with, mount the vCenter Appliance 6.7 U1 ISO file; I'll be doing this from a windows jump host
8. Browse to **vcsa-converge-cli\templates**. Inside this folder are two other folders, with one JSON file in each folder:
    1. **Converge.** This JSON is used to perform the convergence of the PSC services on to the VCSA node, and when doing so also sets up replication between the newly installed PSC services on the VCSA node, and the target PSC. It is important to plan the process based on your environment so you know which node to use as the replication target
    2. **Decommission.** This JSON is used when you are ready to decommission the external PSC nodes after the converge process has successfully run
9. I copied the **converge.json** and the **decommission_psc.json** files to a new folder on my jump box, in **c:\vcsaconverge**. I then renamed the files to put a prefix for the node I will be running vcsa-util against, and then copied the files for me second PSC and VC. So in total, I now have four files in the directory, one for each node in the scope of my deployment:
    * root-psc01-decommission_psc.json
    * root-vc-01-converge.json
    * sitea-psc01-decommission_psc.json
    * sitea-vc01-converge.json
10. I opened up each JSON file and entered the relevant information. The JSON files are relatively straight forward and [This page on the VMware Docs site](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vcenter.install.doc/GUID-A1FED951-0286-4460-B578-F139E49CD72D.html) also has all of the information you should need to accurately fill out the JSON file
    * **Note:** I did have a question specific to my topology and the JSON files. decommission_psc.json has a section described with the following: "_This section describes the embedded vCenter appliance which is in replication with the provided PSC_". But there may be times where you are decommissioning a PSC that is not directly replicating with an embedded VCSA. You can specify any embedded vCenter Server in the SSO domain that is replicating with the PSC you are decommissioning and my understanding is that you do not need to specify an embedded vCenter server that has a direct replication agreement with the PSC you are aiming to decommission. I believe you need to specify an embedded VCSA as it uses cmsso-util on that node to decommission the target PSC


11. So I'm now in a position where I can start running **vcsa-util** to start the converge and decommission. Open up a command prompt / shell, depending on the system you are running this process from, and navigate to the relevant subfolder in the **vcsa-converge-cli** folder. For me, the ISO is mounted as E:\ in Windows, so I've changed my directory to **E:\vcsa-converge-cli\win32**
12. From here you can run either of the following commands to get the help for both the converge and decommission procedures

    ```
    .\vcsa-util.exe converge --help
    ```
    ```
    .\vcsa-util.exe decommission --help
    ```


13. The first step I want to take is to converge my first vCenter Server, sitea-vc01.lab.virtualtassie.com. This will install the PSC RPMs on my VCSA and establish a replication agreement between the new embedded PSC and the external PSC, as pictured below (the red arrows will show the PSC replication agreements):

[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_3.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_3.png)

14. The command I ran for this step is:

    ```
    .\vcsa-util.exe converge --no-ssl-certificate-verification --backup-taken C:\vcsaconverge\sitea-vc01-converge.json
    ```


15. And the output was as follows:
    ```
    Run the installer with "-v" or "--verbose" to log detailed information
    The certificate of server 'sitea-esxi01.lab.virtualtassie.com' will not be
    verified because you have provided either the
    '--no-ssl-certificate-verification' or '--no-esx-ssl-verify' command parameter,
    which disables verification for all certificates. Remove this parameter from the
    command line if you want server certificates to be verified.
    [01/18] [SUCCEEDED] Precheck validations for converge
    [02/18] [SUCCEEDED] Gather requirements
    [03/18] [SUCCEEDED] Leave federation domain
    [04/18] [SUCCEEDED] Uninstall vmafd client
    [05/18] [SUCCEEDED] Stop all services
    [06/18] [SUCCEEDED] Initialize converge
    [07/18] [SUCCEEDED] Update node type to embedded
    [08/18] [SUCCEEDED] Install required RPMs
    [09/18] [SUCCEEDED] Run vmafd firstboot
    [10/18] [SUCCEEDED] Retain machine ID and LDU
    [11/18] [SUCCEEDED] Handle vmdir state
    [12/18] [SUCCEEDED] Verify replication complete
    [13/18] [SUCCEEDED] Run vmon, rhttpproxy, lookupsvc firstboot
    [14/18] [SUCCEEDED] Run vmidentity-firstboot
    [15/18] [SUCCEEDED] Update certificates
    [16/18] [SUCCEEDED] Run license_firstboot Firstboot
    [17/18] [SUCCEEDED] Starting all services on converged VCSA node
    [18/18] [SUCCEEDED] Cleanup after converge
    
    Converged to VCSA with embedded PSC successfully!
    
    You may proceed with next step according to the documentation at https://docs.vmware.com/en/VMware-vSphere/index.html for your topology or PSC HA configuration
    VC joined to AD Domain.
    DNS list updated to have local caching ip 127.0.0.1
    
    
    This machine has to be rebooted to finish the operation. Reboot now?Press (Y|y)es to proceed: y
    Machine reboot started. vCenter should be available in few minutes
    ```


16. It is at this point in time that I'm going to re-register my NSX manager with the now embedded VCSA. I'm not going to write the steps on how to do this, but this would be a good point in time to re-register any solutions that are registered with the external PSC, as we will be decommissioning that external PSC shortly. So my topology now looks like:
[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_4.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_4.png)

17. The next step is to run the converge tool against my second vCenter Server, **root-vc01.lab.virtualtassie.com**. In the JSON file for the second vCenter Server, I have entered the replication server as being the first VCSA I ran the converge tool against, which is now embedded. This creates a linear replication agreement, and also means I will have configured the replication agreement that I need to be in place after I remove the external PSC nodes. The topology I will have after this converge is shown in the image below:
[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_5.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_5.png)

18. Similar to before, the command I ran for this step is:

    ```
    .\vcsa-util.exe converge --no-ssl-certificate-verification --backup-taken C:\vcsaconverge\root-vc01-converge.json
    ```

19. If we now take a step back, I'm in a position where I have two embedded VCSA nodes, with a replication agreement between them, my external solution (NSX Manager) has been re-registered with my embedded VCSA, and I still have a replication agreement in place between the embedded node and the external node(s)
20. It is now time to decommission the external PSC nodes to complete the converge process. First I am going to decommission the PSC that is not replicating with my embedded nodes, as shown in the diagram below:
[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_6.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_6.png)

21. The command being run this time is the 'decommission' function of vcsa-util.exe. Back in step 9 I have already created the decommission.json files for both external PSC's. Again the information is relatively straight forward, and the "replicating VC" can be any VC with an embedded PSC that is replicating with the external PSC being decommissioned, either directly or indirectly. The command I ran to decommission the first external PSC:

    ```
    .\vcsa-util.exe decommission --no-ssl-certificate-verification C:\vcsaconverge\root-psc01-decommission_psc.json
    ```


22. And the output was as follows:

    ```
    Run the installer with "-v" or "--verbose" to log detailed information
    The certificate of server 'pesxi.lab.virtualtassie.com' will not be verified
    because you have provided either the '--no-ssl-certificate-verification' or
    '--no-esx-ssl-verify' command parameter, which disables verification for all
    certificates. Remove this parameter from the command line if you want server
    certificates to be verified.
    Precheck PSC decommission task successful.
    CONVERGE_PSC_HOSTNAME:
    root-psc01.lab.virtualtassie.com
    Precheck vCenter decommission task successful.
    PSC machine powered off successfully.
    Decommissioning PSC node. This may take some time. Please wait..
    
    Successfully decommissioned the PSC node
    
    =================================== 10:14:43 ===================================
    Result and log file information...
    WorkFlow log directory:
    C:\Users\matt.LAB\AppData\Local\Temp\vcsaCliInstaller-2018-11-14-10-05-oc8t3pfn\workflow_1542189906363
    ```


23. At this point in time my environment looks like this:
[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_7.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_7.png)

24. And it is now time to decommission that last external PSC:
[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_8.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_8.png)

25. Again, to do this we use the decommission function of vcsa-util:

    ```
    .\vcsa-util.exe decommission --no-ssl-certificate-verification C:\vcsaconverge\sitea-psc01-decommission_psc.json
    ```

Finally I am now at the point of reaching my desired state, where I have converged both external vCenter and PSC's in to embedded vCenter Servers that are replicating with each other and using embedded linked mode. Both external PSCs have been decommissioned from the SSO domain and the PSC virtual machines have been powered off, ready to be deleted. Because I didn't use the skip option, my vCenter Servers were added to Active Directory and I have confirmed AD authentication is working as expected using AD Integrated auth. As planned, my environment now looks like this:

[![](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_2.png)
](/wp-content/uploads/2018/11/VCSA_ConvergeWalkthrough_2.png)


Overall with my limited use, the converge tool has worked quite well for an initial release. I did originally spend quite a lot of time troubleshooting the issue with having the VCSA file backed backup scheduler enabled, but apart from that it has been relatively smooth sailing. As we upgrade customers to 6.7U1 and later we will certainly be planning to converge their environments to streamline operations and management moving forward.

Have you ran the converge tool in your lab or work environment yet? How did you go?