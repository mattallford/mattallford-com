---
title: "Brave Browser: Azure Portal - Something Went Wrong"
date: 2021-01-07
author: Matt Allford
url: /2021/Brave-Browser-Azure-Portal-Something-Went-Wrong/
categories:
- '2021'
- January
- Azure

tags:
- Azure
- Microsoft
- Brave
- Browser
---

**TL;DR** - *In the last few days (start of 2021), the "Shields Up" feature in the Brave web browser seems to be flagging a JSON file as a cross-site tracker when creating a resource in Azure, causing the blade to not load. To work around this, you can go "shields down" for portal.azure.com.*


I logged on to my machine yesterday, same as any other day, and went to start deploying some resources to Azure via the Azure Portal for a customer, nothing out of the ordinary. I normally use the [Brave](https://brave.com/) browser, which for me has a number of built in benefits. Though, when I tried to deploy a resource, I was faced with a basic error message:

```
Oops!

Could not create Storage account

Something went wrong while creating Storage account.
```
Here's a screenshot of the message:

[![](/post/images/brave-browser-azure-something-went-wrong-01.png)](/post/images/brave-browser-azure-something-went-wrong-01.png)

This wasn't happening for just one resource, either. It was happening when trying to deploy any resource using the portal wizard:

[![](/post/images/brave-browser-azure-something-went-wrong-02.png)](/post/images/brave-browser-azure-something-went-wrong-02.png)


As I've come to find out, behind the scenes this appears to be called the 'GalleryLaunchCreateFailedBlade'! By turning on the developer tools within the browser, I was able to see the following information:

[![](/post/images/brave-browser-azure-something-went-wrong-03.png)](/post/images/brave-browser-azure-something-went-wrong-03.png)


Here's the above in a code block:

```
aVHYxaC0wV-t.js:10 [Microsoft_Azure_Marketplace]  7:18:29 PM MarketplaceExtension/Store/CreateLauncher CreateLauncher: An error occurred in launching the create flow:

Failed to fetch the UI definition for Gallery Item 'Microsoft.VirtualNetwork'. With error:

'{"type":"MsPortalFx.Errors.AjaxError","baseTypes":["MsPortalFx.Errors.AjaxError","MsPortalFx.Errors.Error"],"data":

{"uri":"https://catalogartifact.azureedge.net/publicartifacts/Microsoft.VirtualNetwork-ARM-1.1.0/UIDefinition.json","type":"GET","pathAndQuery":"","requestId":

"cd001e5a-dcb8-42eb-bcc1-e9bf452a1001","failureCause":"","sessionId":"3612aabb0fe84355b43a7972afc1be39","status":0,"statusText":"error","duration":6.374999997206032},

"extension":"Microsoft_Azure_Marketplace","errorLevel":0,"timestamp":5724.885000032373,"name":"Error","innerErrors":[],"jqXHR":

{"readyState":0,"status":0,"statusText":"error"},"textStatus":"error","errorThrown":""}'. Failed with the following error: 

{"extension":"Microsoft_Azure_Network","maretplaceItemId":"Microsoft.VirtualNetwork-ARM"} 

(Launching the 'GalleryLaunchCreateFailedBlade' blade instead.) []
```


Brave has a feature named "shields", and by default this is enabled, also known as "shields up". What does it do?

*"Shields protects your privacy as you browse by making you harder to track from site to site. Many sites include all kinds of trackers which can follow you across the Web. Shields blocks this type of content, keeping you safe and even increasing your browsing speed."*

I noticed on the shield icon that a few items had been blocked from running, and when I opened it, the items being blocked were on portal.azure.com, classified under "trackers & ads":

[![](/post/images/brave-browser-azure-something-went-wrong-04.png)](/post/images/brave-browser-azure-something-went-wrong-04.png)

After dropping down the menu for more information, it provided me with the specific URLs to the items that were blocked and being cross-site trackers, which were:

```
https://catalogartifact.azureedge.net/publicartifacts/Microsoft.VirtualMachine-ARM-1.1.0/UIDefinition.json
https://catalogartifact.azureedge.net/publicartifacts/Microsoft.VirtualNetwork-ARM-1.1.0/UIDefinition.json
https://catalogartifact.azureedge.net/publicartifacts/Microsoft.StorageAccount-ARM-3.1.0/UIDefinition.json
```

Looking at these JSON files, they just appear to contain the configuration to launch the correct blade for the relevant resource, but my guess is that due to it being pulled from a different domain, that the shields feature in Brave is flagging this and blocking it from running.

You do have the ability to go "shields down" for a particular site, which can be done by the toggle when clicking on the shield in brave, and immediately after doing this, the blade will load as expected, allowing you to continue creating your resource.