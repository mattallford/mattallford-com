---
author: Matt Allford
date: 2016-01-11 01:37:53+00:00
draft: false
title: VMWare Update Manager sysimage.fault.SSLCertificateError
type: post
url: /2016/vmware-update-manager-sysimage-fault-sslcertificateerror/
categories:
- '2016'
- January
tags:
- Cert
- Certificate
- manager
- SSL
- update
- VUM
---

Recently I've gone through the process of replacing the machine_ssl certificates on our vCenter and PSC nodes at work, and shortly after I went to use Update Manager and received the following error: sysimage.fault.SSLCertificateError

[![VUM_1](/wp-content/uploads/2016/01/VUM_1-300x87.png)
](/wp-content/uploads/2016/01/VUM_1.png)
We opted for the 'Hybrid' model of certificates in vSphere 6, where the machine_ssl certificate on the PSC and VC server nodes is replaced with an externally signed certificate, and the VMCA takes care of all of the solution user certificates using the default configuration.

More information available here - [https://blogs.vmware.com/vsphere/2015/07/custom-certificate-on-the-outside-vmware-ca-vmca-on-the-inside-replacing-vcenter-6-0s-ssl-certificate.html](https://blogs.vmware.com/vsphere/2015/07/custom-certificate-on-the-outside-vmware-ca-vmca-on-the-inside-replacing-vcenter-6-0s-ssl-certificate.html)
After changing the SSL certificates, VUM needs to be re-registered with the vCenter server using the steps below.

1. Log in to the server where VUM has been installed and launch **VMwareUpdateManagerUtility.exe** from **C:\Program Files (x86)\VMware\Infrastructure\Update Manager**
2. As instructed, enter the vCenter Server IP / name, and the credentials that VUM uses to connect to the vCenter Server and click log in
3. When the VUM Utility is logged in, there is an option to **Re-register to vCenter Server**. Click on this and again enter the vCenter Server IP / Name, and the credentials that VUM uses to connect to the vCenter server and click Apply
4. You should see an '**Applying configuration...**' status at the bottom of the utility, and then a prompt to restart the VUM service to apply the setting
5. Restart the VMWare vSphere Update Manager Service

After these steps are complete, log out of the web client and log back in. VUM should now connect to the vCenter server successfully.

[![VUM_2](/wp-content/uploads/2016/01/VUM_2-300x152.png)
](/wp-content/uploads/2016/01/VUM_2.png)
