---
author: Matt Allford
date: 2017-06-09 10:37:22+00:00
draft: false
title: PowerCLI Script to Power On or Off vSphere Lab
type: post
url: /2017/powercli-script-to-power-on-or-off-vsphere-lab/
categories:
- '2017'
- June
tags:
- lab
- powercli
- powershell
- script
- set-labvmpowerstate
- VMware
- vsphere
---

I was recently chatting to a few guys regarding powering on and off VMs within a vSphere lab and using a script to do so in some sort of controlled manner (or force power off everything if desired). A few of us had a quick search, and couldn't see anything that met the requirements of being able to power on and power off the lab, as well as place VMs into some sort of priority group.

So last Friday I sat down and wrote a script to do just this. I've made a few minor tweaks over the past week and am happy to now consider this a version 1.0. There will be additional features and functionality added, so if you spot something, please don't hesitate to ask (or even better, put in a PR to the project on GitHub!). At this point in time, the requirements I was writing this around were as follows:

* Ability to execute against a single ESXi host or a vCenter server
    * Note that at this point in time, the parameter on the script is -TargetESXiHost, but I believe this will work OK if you insert the IP / DNS for a vCenter Server
* Ability to organise virtual machines in the environment into some sort of priority grouping using metadata. Due to the above and my testing against ESXi 6.5, I was unable to use tags or folders within the inventory, so I ended up creating an advanced property on virtual machines, which gets set with a value of 1 through to 5. If you know of a better way, please let me know
* Ability to easily configure the priority group on virtual machines - the script needed to provide a method to do this and not rely on someone doing pre-req work
* Ability to Power on the lab (only machines with a priority group defined will be powered on)
* Ability to Power off the lab (Option to do it cleanly via VMtools with a wait time and then hard power off, or just hard power off immediately)
* Ability to reset (delete) the advanced setting on all virtual machines to remove any config used by this script from the environment
* Be simple to run. All you need is PowerCLI 6.5 and the script downloaded from Github

The script was tested with PowerCLI 6.5 R1 against an ESXi 6.5 server.

And with that, I present [Set-LabVMPowerState](https://github.com/mattallford/Set-LabVMPowerState).

You can go and grab the script from GitHub at the link above. I've written a bit in the readme.md at Github, including some information about the script, parameters and some example usage. I think I've covered off all of the current use cases in the examples.

I'll take you through a few of these examples with screenshots from my lab below.

Enjoy, and as I said, please hit me in the comments below, GitHub or Twitter with any comments or feedback!

```PowerShell
Set-LabVMPowerState -ConfigurePriority -TargetESXiHost 10.0.0.171
```

Running this will prompt you to answer if you want to set a priority group on each VM in the target environment, and if so you then need to specify the priority group number.

[![](/wp-content/uploads/2017/06/LabVMPowerState_1.png)
](/wp-content/uploads/2017/06/LabVMPowerState_1.png)

```Powershell
Set-LabVMPowerState -ConfigurePriority -SkipConfiguredVMs&nbsp;-TargetESXiHost 10.0.0.171
```

Running this will allow you to set the priority groups, but skip any VM that has already been configured. Note the VMs I set a priority group on above are not show in the output below.

[![](/wp-content/uploads/2017/06/LabVMPowerState_2.png)
](/wp-content/uploads/2017/06/LabVMPowerState_2.png)

```powershell
Set-LabVMPowerState -PowerOn -PoweronSleep 30 -TargetESXiHost 10.0.0.171
```

Running this will power on all VMs in the target environment that have a priority group defined, starting with group 1 and working through to group 5, with a sleep time of 30 seconds between powering on each VM as to not create a boot storm.

[![](/wp-content/uploads/2017/06/LabVMPowerState_3.png)
](/wp-content/uploads/2017/06/LabVMPowerState_3.png)



As per below, if a VM in the priority group is already powered on, it will note this in the console and skip that VM:

[![](/wp-content/uploads/2017/06/LabVMPowerState_7.png)
](/wp-content/uploads/2017/06/LabVMPowerState_7.png)





```powershell
Set-LabVMPowerState -PowerOff -PowerOffWaitTime 120 -TargetESXiHost 10.0.0.171
```

Running this will attempt to gracefully power off all VMs in the target environment that have a priority group defined, starting with those in group 5 and working through to group 1, with a sleep time of 120 seconds before moving onto VMs in the next priority group. Any VMs that were not able to be gracefully be powered down will be left running.

[![](/wp-content/uploads/2017/06/LabVMPowerState_4.png)
](/wp-content/uploads/2017/06/LabVMPowerState_4.png)




```powershell  
Set-LabVMPowerState -PowerOff -PowerOffWaitTime 10 -ForcePowerOff -TargetESXiHost 10.0.0.171
```

Running this will attempt to gracefully power off all VMs in the target environment that have a priority group defined, starting with those in group 5 and working through to group 1, with a sleep time of 120 seconds before moving onto VMs in the next priority group. When the sleep time ends, any VMs in the current priority group that are still running will be hard powered off.

[![](/wp-content/uploads/2017/06/LabVMPowerState_5.png)
](/wp-content/uploads/2017/06/LabVMPowerState_5.png)


In the example below, the same command is used but there is a VM named 'Veeam' that does not have VMtools installed. It notes that the clean shutdown attempt failed, and if -ForcePowerOff was specified, the VM will be hard powered off after the sleep is finished.

[![](/wp-content/uploads/2017/06/LabVMPowerState_6.png)
](/wp-content/uploads/2017/06/LabVMPowerState_6.png)
