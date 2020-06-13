---
author: Matt Allford
date: 2016-11-21 12:18:52+00:00
draft: false
title: vCenter 6.5 PSC Repoint Limitations
type: post
url: /2016/vcenter-6-5-psc-repoint-limitations/
categories:
- '2016'
- November
tags:
- cmsso-util
- PSC
- repoint
- site
- SSO
- vc
- vcenter
---

Hi all,

This is just a quick note to let you all know of a limitation with vSphere 6.5. Repointing of a vCenter Server to a Platform Services Controller (PSC) in another vSphere SSO domain SITE is **not supported**. Please see the caution at the top of [this](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2131191) VMware KB article.

**Note:** As of 21/11/2016, the KB article for repointing a VC to a PSC WITHIN the same SSO domain site (see [here](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2113917)) also has a caution to say it is not supported with 6.5. I believe this is a mistake in the KB article, as the 6.5 documentation has steps on how to repoint within the same SSO site, and I've reached out to the product manager Adam Eckerle who also believes this is an error and is investigating.

A quick bit of background for those interested...

In vSphere 5.5, we had the ability to repoint services between SSO sites, domains, etc. It was quite flexible.

In vSphere 6 pre Update 1, we were only given the ability to repoint VC servers within the same SSO Site.

From vSphere 6U1, we were given the ability to repoint VC servers to any PSC in the SSO domain, same site or different site. We could not repoint the vCenter Server to a PSC in a different SSO domain.

And now with 6.5, it seems we have taken a step back again and we're now back to how it was in 6.0GA, which is we can repoint the VC between PSC nodes in the same SSO site, but not cross site and not to a PSC in a different SSO domain.

Because of these limitations and changes with each version, you need to give your upgrade process some thought prior to making any changes. You might need to do some remediation and collapsing work prior to upgrading, as you will find that after you upgrade you might not have the tools and abilities at your disposal to reconfigure the architecture to your desired state.
