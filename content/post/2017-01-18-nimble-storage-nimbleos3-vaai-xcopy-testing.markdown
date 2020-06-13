---
author: Matt Allford
date: 2017-01-18 13:01:57+00:00
draft: false
title: Nimble Storage NimbleOS3 VAAI XCOPY Testing
type: post
url: /2017/nimble-storage-nimbleos3-vaai-xcopy-testing/
categories:
- '2017'
- January
tags:
- array
- esxi
- Nimble
- nimbleos
- san
- Storage
- VAAI
- VMware
- xcopy
---

# Introduction


Nimble Storage arrays are one of my favourite SAN arrays to work with at the moment. However, until Nimble OS3 which was made GA on the 31st of August, 2016, there was no support for the XCOPY VAAI primitive. This meant that when performing actions such as a storage vMotion that moved data between datastores on the same array, or cloning virtual machines, the ESXi host performing the action was required to read the data from the array up through the ESXi host, and then write it back to the array, creating additional load on the ESXi host and traffic on the storage adapters and network.

For arrays that support the XCOPY VAAI primitive, the ESXi host instead would send a command to the array telling it to move the data and the array will do the heavy lifting. Thankfully Nimble OS3 brings support for the XCOPY VAAI primitive and I'm lucky enough to have access to an environment currently running Nimble OS2 that will be upgrading to 3 shortly, so I wanted to take the opportunity to do a few tests.

There is a blog on the Nimble Connect forums [here](https://connect.nimblestorage.com/people/ndyer/blog/2016/08/31/nimbleos-3-vmware-copy-offload-xcopy-supported) that goes into this in more detail and includes an explanation of pre-XCOPY support (NimbleOS2 and below) and post-XCOPY support (NimbleOS3).

I was also interested to see if there was any speed increase to storage vMotion or virtual machine cloning by offloading it to the array. My findings for this are in the summary.

I'm running the tests in the environment using NimbleOS2 and then compare it after upgrading the array to OS3 and making no other changes to the configuration. As noted in the Nimble blog, the configuration of the data mover can be changed from 4MB to 16MB. I did get time to test with the config set to 16MB, but in fairness the tests I were running were not going to benefit from this config change. I decided to include the results anyway, which are in the table located in the summary. All tests were run at least 3 times at different times of different days to get an average.


# VAAI XCOPY and Windows Powered On Machines


In my testing, I was using a virtual machine running Windows Server 2012R2. After the upgrade to NimbleOS3, I did some testing for storage vMotion and cloning. The storage vMotion was being offloaded successfully, but the VM clone was not. After a puzzling few hours, I came across the following post from Cody Hosterman at Pure Storage.

**Note**: Cody does mention that both storage vMotion and clone operations are not offloaded for powered-on machines. In my testing, the storage vMotion was being offloaded fine, but the VM clone was not.

http://www.codyhosterman.com/2016/06/vaai-xcopy-not-being-used-with-powered-on-windows-vm/

In short, by default when cloning a powered on Windows virtual machine with VMtools installed, the clone operation will not be offloaded to the array using VAAI. This is due to an advanced option disk.EnableUUID being set to true by default, which allows VMtools to quiesce the virtual disks. This also prevents VAAI XCOPY from being used. Check out Cody's post for additional information.

When I did the clone test of the virtual machine running NimbleOS3, I did this when the machine was powered off, allowing VAAI XCOPY to be used.


# Environment

* Nimble Storage CS300 array
* Dell R730 ESXi host running 6.0U2
* iSCSI running over 10GB networking
* 2 dedicated NICs on the R730 (vmnic1 and vmnic5) using the iSCSI software initiator and Nimble Connection Manager
* Virtual machine running Windows Server 2012R2 with 2 disks. 70GB for OS and 110GB for data drive, both thick provision eager zero on the same LSI Logis SAS controller. Data drive has 100GB of actual data stored on it



# Test A (Storage vMotion)


With this test I performed a storage vMotion of the virtual machine from datastore 1 to datastore 2, both hosted on the same CS300 array.


## NimbleOS2


While the array was running NimbleOS2, XCOPY offload was not enabled and therefore we can see the traffic traversing the network adapters on the ESXi host as it reads the data from the array and then writes the data back to the same array.

Here's a screenshot of the traffic on the vmnic physical NICs on the server during the move:

[![](/wp-content/uploads/2016/12/NMBL_Test_1-1024x594.png)
](/wp-content/uploads/2016/12/NMBL_Test_1.png)

And here's a screenshot of the array performance dashboard:[![](/wp-content/uploads/2016/12/NMBL_TEST_1_Array.png)
](/wp-content/uploads/2016/12/NMBL_TEST_1_Array.png)


## NimbleOS3


After the array was upgrade to NimbleOS3, XCOPY offload support is now enabled. When doing the storage vMotion, we see there is no traffic increase on either the ESXi host or the Nimble array. As you can see below, the data move has been offloaded to the array. Because it was not obvious, I've highlighted the timestamp in the screen shots where the storage vMotion was actioned. You'll notice the low data rates in both the host and storage screenshots.

Here's a screenshot of the traffic on the vmnic physical NICs on the server during the move:

[![](/wp-content/uploads/2016/12/NMBL_Test_1_Take33.0-1024x624.png)
](/wp-content/uploads/2016/12/NMBL_Test_1_Take33.0.png)

And here's a screenshot of the array performance dashboard:[![](/wp-content/uploads/2016/12/NMBL_TEST_1_Array_Take3-833x1024.png)
](/wp-content/uploads/2016/12/NMBL_TEST_1_Array_Take3.png)


# Test B (VM Clone)


With this test I performed a clone of the virtual machine from datastore 1 to datastore 2, both hosted on the same CS300 array.


## NimbleOS2


So as a reminder, when the array was running NimbleOS2, XCOPY offload was not enabled and therefore we can see the traffic traversing the network adapters on the ESXi host as it reads the data from the array and then writes the data back to the same array during the clone operation.

Here's a screenshot of the traffic on the vmnic physical NICs on the server during the clone:

[![](/wp-content/uploads/2016/12/NMBL_Test_2-1024x594.png)
](/wp-content/uploads/2016/12/NMBL_Test_2.png)

And here's a screenshot of the array performance dashboard:

[![](/wp-content/uploads/2016/12/NMBL_TEST_2_Array.png)
](/wp-content/uploads/2016/12/NMBL_TEST_2_Array.png)


## NimbleOS3


Again after the NimbleOS upgrade, XCOPY offload is enabled so the clone operation is simply an instruction sent from the ESXi host to the array, and the array does the heavy lifting. Again, I've highlighted the timestamps in the screenshots that show the duration of the VM clone operation.

**Note: Just prior to the highlight below you see a burst of traffic. This was when I tested the VM clone with a powered machine. The highlighted section was when the machine was being cloned while powered off, and therefore using VAAI XCOPY.**

Here's a screenshot of the traffic on the vmnic physical NICs on the server during the clone:

[![](/wp-content/uploads/2017/01/NMBL_Clone_3.0_ESXi-1024x648.png)
](/wp-content/uploads/2017/01/NMBL_Clone_3.0_ESXi.png)

And here's a screenshot of the array performance dashboard:

[![](/wp-content/uploads/2017/01/NMBL_Clone_3.0_Array.png)
](/wp-content/uploads/2017/01/NMBL_Clone_3.0_Array.png)


# Summary


As mentioned in the introduction, I performed a number of tests and times the average time to complete a storage vMotion and VM clone when running both NimbleOS2 and NimbleOS3. My findings are in the table below:

[table id=3 /]

As you can see, storage vMotion activities were on average 4 minutes and 22 seconds faster and VM clones were on average 5 minutes and 7 seconds faster when running NimbleOS3 and making use of VAAI. As well as being faster, there is also the benefit of no CPU overheard for the ESXi host and no additional traffic traversing the storage adapters and storage network.

Overall I think this is a welcome addition to the capabilities of the Nimble fleet and is a good reason on its own to upgrade your arrays from NimbleOS2 to NimbleOS3. There are of course some [other features](https://connect.nimblestorage.com/docs/DOC-1801) of NimbleOS3 that might be appealing to you if you are still running NimbleOS2.
