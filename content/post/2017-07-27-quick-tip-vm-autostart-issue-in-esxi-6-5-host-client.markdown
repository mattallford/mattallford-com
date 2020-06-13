---
author: Matt Allford
date: 2017-07-27 06:12:41+00:00
draft: false
title: Quick Tip - VM Autostart Issue in ESXi 6.5 Host Client
type: post
url: /2017/quick-tip-vm-autostart-issue-in-esxi-6-5-host-client/
categories:
- '2017'
- July
tags:
- Autostart
- EHC
- Embedded Host Client
- esxi
- VMware
---

I've just been working through automating the powering on and off of my lab server, which is a single physical server running ESXi 6.5, and nested virtual machines for my lab. I wanted to use the ESXi Autostart feature to start my domain controller and jump box at power on. But I had an issue where one of the two VMs I want to power on would simply not start.

On the host, I'd gone to **Manage** > **Autostart** > **Edit Settings** and enabled the autostart feature. I then went to the virtual machines view, and you can right click on the column headings and choose **Select Columns** > **Autostart** order to see the autostart order for all VMs on the host. My DC was listed with autostart order '1', and the jump host with '2', but no matter what I did (changed orders, turned autostart off and back on on the host, disabled autostart on the VM and reconfigured it), only my DC would start and my jump box would not.

I sat down a few nights ago and did some searching and found people with similar issues.

The long story short is that if you are facing an issue where you are configuring autostart correctly, but the VM is not starting, simply unregister the VM from the inventory of the ESXi host, and then add it back in to the inventory, which fixes the autostart issue. I'm running ESXi 6.5.0 build 5310538, so this may get fixed in a later release.

On a side note, I did some digging around and found the following file on the host:

**/etc/vmware/hostd/vmAutoStart.xml**

When we take a look at the XML below, it has the system default settings at the bottom, and then an entry for each of the VMs that I have set to autostart. I forgot to save the output, but when this was in a 'broken' state, the <startAction> for my jump box was not set to PowerOn.

So I'm sure if you felt so inclined, you could determine the moid (Managed Object ID) of the virtual machine and add it into this XML file with the relevant settings, or go ahead and continue to use the Embedded Host Client.

```
<ConfigRoot>
  <AutoStartOrder>
    <_length>2</_length>
    <_type>vim.host.AutoStartManager.AutoPowerInfo[]</_type>
    <e id="0">
      <_type>vim.host.AutoStartManager.AutoPowerInfo</_type>
      <key>
        <_type>vim.VirtualMachine</_type>
        <moid>39</moid>
      </key>
      <startAction>powerOn</startAction>
      <startDelay>-1</startDelay>
      <startOrder>1</startOrder>
      <stopAction>systemDefault</stopAction>
      <stopDelay>-1</stopDelay>
      <waitForHeartbeat>systemDefault</waitForHeartbeat>
    </e>
    <e id="1">
      <_type>vim.host.AutoStartManager.AutoPowerInfo</_type>
      <key>
        <_type>vim.VirtualMachine</_type>
        <moid>1</moid>
      </key>
      <startAction>powerOn</startAction>
      <startDelay>120</startDelay>
      <startOrder>-1</startOrder>
      <stopAction>systemDefault</stopAction>
      <stopDelay>120</stopDelay>
      <waitForHeartbeat>no</waitForHeartbeat>
    </e>
  </AutoStartOrder>
  <SystemDefaults>
    <_type>vim.host.AutoStartManager.SystemDefaults</_type>
    <enabled>true</enabled>
    <startDelay>120</startDelay>
    <stopAction>powerOff</stopAction>
    <stopDelay>120</stopDelay>
    <waitForHeartbeat>false</waitForHeartbeat>
  </SystemDefaults>
</ConfigRoot>
```