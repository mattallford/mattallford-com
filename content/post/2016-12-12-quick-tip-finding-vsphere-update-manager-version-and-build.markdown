---
author: Matt Allford
date: 2016-12-12 11:29:11+00:00
draft: false
title: Quick Tip - Finding vSphere Update Manager Version and Build
type: post
url: /2016/quick-tip-finding-vsphere-update-manager-version-and-build/
categories:
- '2016'
- December
tags:
- Update Manager
- VMware
- vsphere
- VUM
---

Every now and then I need to find the version and build number of VMware vSphere Update Manager that is running in a customer's environment. I've written this post mainly as a reminder to myself, but hopefully it helps someone else down the track.

VMware have a [KB article](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1014508) for most of their products to correlate the build numbers with versions. The specific article for VUM is [KB 2143837](https://kb.vmware.com/selfservice/search.do?cmd=displayKC&docType=kc&docTypeID=DT_KB_1_1&externalId=2143837). Sometimes these aren't always right up to date unfortunately.

Whenever I'm looking for the VUM build in a customers environment, I usually head into Add/Remove programs in control panel, and then shortly afterwards remember that this doesn't actually list the normal major version and build, but instead you will see something like this:

[![vum_version_1](/wp-content/uploads/2016/12/VUM_Version_1.png)
](/wp-content/uploads/2016/12/VUM_Version_1.png)

As you may notice, 6.0.0.29963 does not correlate to any of the builds listed in the KB article.

From here I usually head into the main VUM log file, which contains the build version in the first line. The logs are usually kept in the following folder, but this might differ depending on the OS / version you are working with:

C:\ProgramData\VMware\VMware Update Manager\Logs

In here I look for the latest vmware-vum-server-x.log file (where x is a number):

[![vum_version_2](/wp-content/uploads/2016/12/VUM_Version_2.png)
](/wp-content/uploads/2016/12/VUM_Version_2.png)

After opening it up, the first line will list the full version and build (see below), which we can then then use to cross check in the original KB article I linked to. In this instance below, we're looking at an install of VUM 6.0 U2.

[![vum_version_3](/wp-content/uploads/2016/12/VUM_Version_3.png)
](/wp-content/uploads/2016/12/VUM_Version_3.png)

As I was writing this post, I did a quick search and about the only other thing I came across was [this post on Reddit](https://www.reddit.com/r/vmware/comments/3nu9b1/vum_version/), in which one of the posters pointed out that you can also check the details on a .exe file to find the version and build. The particular file this user pointed out was:

C:\Program Files (x86)\VMware\Infrastructure\Update Manager\vmware-updatemgr.exe

Right click on the above file > Properties > Details Tab and you will see the following information:

[![vum_version_4](/wp-content/uploads/2016/12/VUM_Version_4.png)
](/wp-content/uploads/2016/12/VUM_Version_4.png)

Unfortunately I haven't had a chance to play with vSphere 6.5 yet, but VUM is now included and enabled by default in the VCSA which is easily the preferred deployment method for VC with the additional features added in 6.5. I'll need to confirm, but I assume that for VUM on the VCSA, you'll be able to determine the build number by confirming the version of vCenter itself, as the VUM component will be updated as patches and updates are applied to the appliance.
