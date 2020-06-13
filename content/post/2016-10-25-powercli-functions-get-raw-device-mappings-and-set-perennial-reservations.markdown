---
author: Matt Allford
date: 2016-10-25 21:57:52+00:00
draft: false
title: PowerCLI Functions - Get Raw Device Mappings and Set Perennial Reservations
type: post
url: /2016/powercli-functions-get-raw-device-mappings-and-set-perennial-reservations/
categories:
- '2016'
- October
tags:
- device
- function
- Perennially
- powercli
- rdm
- VMware
---

# Introduction

Just a quick post to say I've written a couple of powershell functions to deal with Raw Device Mappings and perennial reservations within a vSphere environment. Although I do try to avoid them, some customers do still have RDMs in their environment, mostly for Microsoft failover clusters configured at the virtual machine layer. Some ad-hoc scripts exist for this at the moment, but I wanted to break out the 'getting' of RDMs and then setting them to perennially reserved.

This applies to ESXi hosts running 5.1 or later.

Only fully tested using PowerCLI 6.3 R1 and uses -V2 of get-esxcli in the Set-DevicePerennialReservation function. PowerCLI is backwards compatible, check out the following URL:

[Which PowerCLI Version Is Right For Me?](http://blogs.vmware.com/PowerCLI/2016/06/2779.html)

For more information, see the following KB article for setting perenially reserved flag:

[KB1016106](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1016106)


# Get-RDMDevice


Find it on Github [here](https://github.com/mattallford/Get-RDMDevice).

Get-RDMDevice is a powershell function that gets all RDM devices in a vSphere environment that are currently in use by virtual machines This can be used to export the device details, or it can be piped to another function I wrote, Set-DevicePerennialReservation, to set the perennial reservation to either true or false

Requires the VMware PowerCLI cmdlets - Always run the latest where possible

This is a function, so dot source the PS1 which will then enable you to use the function. For example:

1. Download and save the ps1 to c:\scripts
2. Run the following to dot source the script and import the function: . C:\Scripts\Get-RDMDevice.ps1
3. You can now run Get-RDMDevice



## Examples
```powershell
Get-RDMDevice
```

This will return all RDM devices connected to virtual machines in the environment



```powershell
Get-RDMDevice -Location Cluster1
```

This will return all RDM devices connected to virtual machines located in Cluster1


# Set-DevicePerennialReservation


Find it on Github [here](https://github.com/mattallford/Set-DevicePerennialReservation).

Set-DevicePerennialReservation is a powershell function that sets the perennial reservation for devices specified in the ScsiCanonicalName paramater to either true or false This function can be piped to from another function I have writted, Get-RDMDevice

Requires the VMware PowerCLI cmdlets - Always run the latest where possible

This is a function, so dot source the PS1 which will then enable you to use the function. For example:

1. Download and save the ps1 to c:\scripts
2. Run the following to dot source the script and import the function: . C:\Scripts\Set-DevicePerennialReservation.ps1
3. You can now run Set-DevicePerennialReservation



## Examples
```powershell
Set-DevicePerennialReservation -ScsiCanonicalName naa.60003ff44dc75adcb00b344794826ba4 -ESXiHosts ESXi01 -PerenniallyReserved:$true
```

Sets the device naa.60003ff44dc75adcb00b344794826ba4 on ESXi host ESXi01 to perenially reserved

```powershell
Set-DevicePerennialReservation -ScsiCanonicalName naa.60003ff44dc75adcb00b344794826ba4 -ESXiHosts (Get-VMHost -Location cluster1) -PerenniallyReserved:$true
```

Sets the device naa.60003ff44dc75adcb00b344794826ba4 on all ESXi hosts in Cluster1 to perenially reserved

```powershell
Get-RDMDevice | Set-DevicePerennialReservation -ESXiHosts (Get-VMHost -Location Cluster1) -PerenniallyReserved:$true
```

Uses another function called Get-RDMDevice to get all RDM devices in the environment, and then pipes that to Set-DevicePerennialReservation to set all of the devices to perenially reserved on all ESXi hosts within the location cluster1
