---
author: Matt Allford
date: 2016-09-20 23:39:39+00:00
draft: false
title: VMware Update Manager vCenter Privileges
type: post
url: /2016/vmware-update-manager-vcenter-privileges/
categories:
- '2016'
- September
tags:
- Update Manager
- vcenter
- vsphere
- VUM
---

I've always tried to use and recommend using the 'least privileges' model when designing and implementing systems. But we've all been there, when the same service account is being used to connect everything to vCenter, and said account has been assigned the full administrator role and propagated within vCenter.

I'm getting my lab set up again and I needed to install VMware Update Manager (VUM). I realised that in the lab previously I have just done as I mentioned before and used a high privilege service account thinking "it's just a lab". I thought this would be a good chance to check what privileges are actually required to install and register VUM with vCenter Server. I've quickly documented the steps below including the only privilege that is required.

* Create a new service account in Active Directory, or your ldap environment. I've called mine **SA-VUM**
* Configure a new role within vCenter server. This role will contain the privilege required to register VUM against the vCenter Server
* Log into vCenter using the vSphere Web Client
* Browse to **Administration** > **Roles** and click the **green plus** to create a new role
* Give the role a name (I've called mine UpdateManager), and the only privilege that is required is **Extension** > **Register Extension**



![VUM_Priv_1](/wp-content/uploads/2016/09/VUM_Priv_1-740x1024.png)


After the role is configured with the required privilege, the role needs to be associated with a user or group and an item in the inventory to create the permission.

* Log in to the vSphere Web Client and go to **vCenter Inventory Lists** > **vCenter Servers** > Select the vCenter Server > **Manage** > **Permissions** and click the **green plus** to create a new permission
* Under **Users and Groups**, click **add** and select the account (SA-VUM in my instance) or group that contains the service account that you want included in the permission
* Under **Assigned Role**, select the role we created earlier (UpdateManager in my instance), and **uncheck** the **propogate to children** option, as the permission only needs to be applied on the vCenter Server object
* Click **OK** to create the permission

[![VUM_Priv_2](/wp-content/uploads/2016/09/VUM_Priv_2-1024x1016.png)
](/wp-content/uploads/2016/09/VUM_Priv_2.png)

The service account now has the correct privilege within vCenter, and it doesn't have any more privileges than it requires!

If you ever plan to uninstall VUM, you may need to come back into the role and add the privilege **Extention** > **Unregister Extension**, so the uninstaller can correctly unregister itself from vCenter.

After VUM has been installed, there will be a new set of privileges available under 'VMware vSphere Update Manager', and these control the privileges within VUM itself. I'd suggest creating a group such as 'VUM Users' or 'VUM Admins' and adding users into this group that require VUM access.

[![VUM_Priv_4](/wp-content/uploads/2016/09/VUM_Priv_4-300x134.png)
](/wp-content/uploads/2016/09/VUM_Priv_4.png)



If the correct permissions are not in place, you will receive the following error when trying to install VUM:

[![VUM_Priv_3](/wp-content/uploads/2016/09/VUM_Priv_3.png)
](/wp-content/uploads/2016/09/VUM_Priv_3.png)

And within the log file vminst.log located in the %temp% directory, the following entries will be found:

```  
VMware Update Manager-build-3545890: 09/20/16 20:28:37 INFO: Reg/UnReg extn command: ["-v vc01.lab.allford.id.au -p 80 -U "SA-VUM" -P *** -S "C:\Program Files (x86)\VMware\Infrastructure\Update Manager\extension.xml" -C "C:\Program Files (x86)\VMware\Infrastructure\Update Manager\\" -L "C:\Users\ADMINI~1.LAB\AppData\Local\Temp\2\\" -O extupdate"]
VMware Update Manager-build-3545890: 09/20/16 20:28:37 AppendPath::done Path: C:\Program Files (x86)\VMware\Infrastructure\Update Manager\vciInstallUtils.exe
VMware Update Manager-build-3545890: 09/20/16 20:28:37 Found "C:\Program Files (x86)\VMware\Infrastructure\Update Manager\vciInstallUtils.exe"
VMware Update Manager-build-3545890: 09/20/16 20:28:49 Process returned 199
VMware Update Manager-build-3545890: 09/20/16 20:28:49 Error:: Unknown VC error
VMware Update Manager-build-3545890: 09/20/16 20:28:49 ERROR: VUM registeration with VC failed
VMware Update Manager-build-3545890: 09/20/16 20:28:49 Posting error message 25085
```

Both of these point to invalid privileges for the account you specified in the VUM installer.


