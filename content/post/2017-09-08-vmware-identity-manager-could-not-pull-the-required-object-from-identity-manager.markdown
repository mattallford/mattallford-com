---
author: Matt Allford
date: 2017-09-08 11:56:55+00:00
draft: false
title: VMware Identity Manager - Could not Pull the Required Object From Identity
  Manager
type: post
url: /2017/vmware-identity-manager-could-not-pull-the-required-object-from-identity-manager/
categories:
- '2017'
- August
tags:
- identity manager
- vidm
- VMware
---

I was recently working in a customers environment to configure vRealize Log Insight with VMware Identity Manager. They had vIDM deployed some time ago and configured, primarily for vRealize Business for Cloud. I'm not yet that familiar with vIDM, and I learnt something new in troubleshooting this issue.

When logging in to vIDM to set up the new configuration, after going to **Identity & Access Management** > **Directories** > Selected the sync directory > **Sync log**,  I noticed there were sync issues with Active Directory:

[![](/wp-content/uploads/2017/09/vIDM_1.png)
](/wp-content/uploads/2017/09/vIDM_1.png)

There wasn't much more information here to show what the issue might be, so I wanted to check out the logs. These can be found under **Appliance Settings** > **VA Configuration** > **Manage Configuration**, log in to the admin portal and then go to **Log File Locations** > **Prepare Log Bundle**. A ZIP file will be prepared which you can then download and extract. Inside the logs bundle is a file named **connector.log**.

Inside this file, I found some warnings as per below

```
2017-08-18 09:20:21,920 WARN (SimpleAsyncTaskExecutor-3) [3002@VMVIDM01;admin@VMVIDM01;10.6.5.26] com.vmware.horizon.directory.ldap.LdapConnector - Failed to connect to domaincontroller.domain.com:null
javax.naming.CommunicationException: domaincontroller.domain.com:389 [Root exception is java.net.ConnectException: Connection timed out (Connection timed out)]

Caused by: java.net.ConnectException: Connection timed out (Connection timed out)
```

Long story short, since deploying vIDM, the customer had changed domain controllers, and vIDM doesn't automatically query the domain for available domain controllers. Instead, a file named **domain_krb.properties** is created when the configuration is first done, and this file contains information about the domain including the names of domain controllers that vIDM should query.

This sentence is from the linked [VMware documentation page](https://pubs.vmware.com/vidm/index.jsp?topic=%2Fcom.vmware.wsair-administration%2FGUID-8D2CCA98-169B-4BD4-9B16-CE6AB2322A77.html) titled "About Domain Controller Selection (domain_krb.properties file)";

```
The domain_krb.properties file determines which domain controllers are used for directories that have DNS Service Location (SRV records) lookup enabled. It contains a list of domain controllers for each domain. **The connector creates the file initially, and you must maintain it subsequently**. The file overrides DNS Service Location (SRV) lookup.
```

To summarise, you'll need to manually update the domain_krb.properties file any time there are changes to domain controllers in the environment that vIDM is using. To do this:

1. Log in to the vIDM appliance with the root account
2. run **vi /usr/local/horizon/conf/domain_krb.properties **and press** enter**
3. Make changes required to update the domain controllers listed for the domain and save the file
4. Change the ownership of the file by running **chown horizon:www /usr/local/horizon/conf/domain_krb.properties**
5. Restart the service by running **service horizon-workspace restart**

After making this change and updating the file to connect to valid domain controllers, I ran a re sync from vIDM and it went through successfully.
