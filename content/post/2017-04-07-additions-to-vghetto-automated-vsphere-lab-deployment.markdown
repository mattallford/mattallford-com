---
author: Matt Allford
date: 2017-04-07 12:04:57+00:00
draft: false
title: Additions To vGhetto Automated vSphere Lab Deployment
type: post
url: /2017/additions-to-vghetto-automated-vsphere-lab-deployment/
categories:
- '2017'
- April
tags:
- automated
- lab
- lab deployment
- vghetto
- VMware
- vsphere
---

So last week I received a new lab server that is going to be housed at home. It is a Dell T430 with 128GB ram, which is exactly what I was after as I want to get right back into the lab work, but I didn't want a heap of gear and network requirements. NestedESXi has worked reasonably well in the past few vSphere releases and there's not much that can't be simulated within a nested environment for the purposes of learning or testing.

With the tools we have available today, there was no way I was going to look at manually building the nested environment. In late 2016, William Lam [released some scripts](http://www.virtuallyghetto.com/2016/11/vghetto-automated-vsphere-lab-deployment-for-vsphere-6-0u2-vsphere-6-5.html) that allow users to deploy a nested vSphere lab in an automated fashion. The scripts leverage the ESXi OVA that William created and uses PowerCLI to orchestrate the deployment. A huge thanks to William for the effort and for releasing this to the community for consumption.

I downloaded the requirements and the script and gave it a whirl, and it worked perfectly. With that said, there were a few additions that I wanted to make to the script. I've gone ahead and done this and made the changes in a [fork from William's repository on Github](https://github.com/mattallford/vghetto-vsphere-automated-lab-deployment).

So far I've only made these changes to the vSphere 6.0 and 6.5 standard deployments (not the self-managed deployments, see William's post above for more information on this). I'm also mindful of not trying to achieve too much in these scripts and blow them out with a lot of code or taking a long time to run. Once the initial lab deployment has been achieved, further configuration of the environment can be performed with additional scripts, or even a configuration management project such as [Vester](https://github.com/WahlNetwork/Vester).

I also added in some of William's changes to the 6.0 deploy script that he recently only added to the 6.5 deploy script, such as support for adding nested ESXi nodes to vCenter using DNS names rather than IP, and adding CPU/MEM/Storage resource requirements in confirmation screen.


## Usage

* Download the ps1 file and JSON file from the Github page linked above. Ensure you have all of the pre-requisites as per William's blog article
* Fill out the JSON file with the desired configuration. Most of the settings are hopefully self explanitory. If you have any queries please reach out. In future I will look at adding some help / comments
* Run the powershell script with the parameter -JSONConfigurationFile to specify the JSON file to use for deployment (see example below).

When running the script, you will be presented with a summary of the deployment options including DNS pre checks as per the below screenshot. As prompted, enter **y** to proceed with the deployment.

[![](/wp-content/uploads/2017/04/65_Lab_Deployment_2.png)
](/wp-content/uploads/2017/04/65_Lab_Deployment_2.png)

During deployment, the status is written to screen and also to the log file. At the end you will be provided with a summary, as per the below screenshot.

[![](/wp-content/uploads/2017/04/65_Lab_Deployment_3.png)
](/wp-content/uploads/2017/04/65_Lab_Deployment_3.png)

And after logging into the target VCSA, you will see the complete deployment with 3ESXi nodes in a VSAN cluster.

[![](/wp-content/uploads/2017/04/65_Lab_Deployment_4.png)
](/wp-content/uploads/2017/04/65_Lab_Deployment_4.png)

The main changes I've made to date are:


## Configuration Externalised in JSON file


I'm not a huge fan of needing to edit powershell scripts and modify variables directly within the script, so I went ahead and created a JSON file that will hold the configuration for the lab deployment. There is now no need to open the ps1 file. All of the configuration is specified in the JSON and the deploy powershell script is pointed to the JSON file to import the configuration.

Due to this, I also added a parameter to the script so it can now be called as follows:

```  
vsphere-6.5-standard-lab-deployment.ps1 -JSONConfigurationFile c:\scripts\65LabDeploy.json
```

Note that the "\" character in JSON is an escape character, so for full UNC paths you need to start with four slashes and then have 2 slashes in the rest of the path. For local paths, use two slashes for each directory. Examples of both are below.

```
"FilePaths": {
    "NestedESXiApplianceOVA": "\\\\10.0.0.100\\iso\\VMware\\ESXi\\6.5\\Nested_ESXi6.5_Appliance_Template_v1.ova",
"VCSAInstaller": "\\\\10.0.0.100\\iso\\VMware\\Appliance\\6.5\\VMware-VCSA-all-6.5.0-4602587",
"NSXOVA": "C:\\Users\\primp\\Desktop\\VMware-NSX-Manager-6.3.0-5007049.ova",
"ESXi65aOfflineBundle": "C:\\Users\\primp\\Desktop\\ESXi650-201701001\\vmw-ESXi-6.5.0-metadata.zip"
}
```

## External or Embedded VCSA

William's script deploys an embedded VCSA with the PSC services running on the same node as the VCSA. This is likely fine for most deployments, but I do have a requirement to deploy VCSA with an external PSC on occasion. Due to this, I've added some configuration within the JSON file that allows you to toggle between deploying an embedded or external VCSA. Sticking with William's theme, this is '1' for True/enable, and '0' for False/disable.

If you would like to deploy an external PSC, set **ExternalPSC** in the JSON config below to 1 and fill out the remaining details for the PSC node. Leaving **ExternalPSC** as 0 will tell the script to deploy embedded VCSA.

```
"PSC": {
    "ExternalPSC": "1",
"Displayname": "PSC65-1",
"IPAddress": "10.0.0.186",
"Hostname": "PSC65-1.lab.virtualtassie.com"
}
```

## DNS Check / Creation and NTP config

As many of you may know, DNS and NTP are critical to successful deployments, especially of the VCSA.

I decided to add a section to the script that will check DNS resolution of all of the nested nodes being deployed.

Additionally, if the DNS server is a Windows Server with remote powershell enabled, and the user you are running the script as has privileges, you can opt to have the script create the DNS records automatically if a DNS lookup is unsuccessful.

Look in the JSON file for the configuration below. **CheckDNS** can be toggled on (1) or off (0). If **CheckDNS** is enabled and you want the script to create DNS records if a lookup fails, then set **WindowsDNSServer** to 1 and then add in the details for the DNS Server name and the DNS zone name to create the record in.

```
"DNSCheck": {
    "CheckDNS": "1",
"WindowsDNSServer": "0",
"DNSServer": "DC01",
"DNSZoneName": "lab.virtualtassie.com"
}
```

In the below screenshot, you can see that records for 2 nodes about to be deployed did not exist and the script went and created them, and checks for 2 other nodes were successful.

[![](/wp-content/uploads/2017/04/Lab_Deploy_DNSCheck.png)
](/wp-content/uploads/2017/04/Lab_Deploy_DNSCheck.png)

Additionally, by default the JSON templates for VCSA deployment provided on the VCSA media in 6.0/6.5 does not specify an NTP server. I've added in configuration that will add the NTP server to the config when deploying the PSC / VCSA.


## Summary

In summary, William has of course done an awesome job with these scripts and has put them out there for people to use and build upon. I added in a few things that were helpful to me and I figured they may be helpful to others as well, so I've put it up on GitHub.

If you have any feature requests, comments or questions, please let me know.
