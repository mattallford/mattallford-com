---
author: Matt Allford
date: 2016-12-30 11:02:53+00:00
draft: false
title: vSphere 6.5 - Upload File to Service Request via Web Client
type: post
url: /2016/vsphere-6-5-upload-file-to-service-request-via-web-client/
categories:
- '2016'
- December
tags:
- support request
- VMware
- vsphere 6.5
- vsphere65
- web client
---

Just a quick note on something I spotted as I've been working through some blog posts for vSphere 6.5. It looks like you can now upload files for a Support Request to VMware directly from the vSphere Web Client.

[KB 1008525](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1008525) lists some of the main methods for uploading diagnostic information to VMware including using the Support Assistant (which I am planning to do a post on), FTP and email. It does not yet contain information for this new feature in vSphere 6.5, but it is documented [here](https://pubs.vmware.com/vsphere-65/index.jsp#com.vmware.vsphere.monitoring.doc/GUID-D03067E9-A285-4755-A0D2-7D631FDF2141.html?resultof=%2522%2575%2570%256c%256f%2561%2564%2522%2520).

The vCenter Server requires access to https://*.vmware.com:443/* for this feature to work.

To use this feature, log into the vSphere Web Client, click on Administration, under support click Upload File to Service Request, and then click the box labelled Upload File to Service Request.

Enter in the SR number and click Choose File and browse to the file you want to upload, and then click OK. The file will then be uploaded to VMware.

A nifty little feature, handy if you are already in the web client and need to upload a file as it will save you having to launch an FTP connection or using another method.

[![](/wp-content/uploads/2016/12/SR_File_Upload-1024x744.png)
](/wp-content/uploads/2016/12/SR_File_Upload.png)
