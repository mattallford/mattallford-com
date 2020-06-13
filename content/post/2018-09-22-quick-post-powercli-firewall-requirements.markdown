---
author: Matt Allford
date: 2018-09-22 02:17:29+00:00
draft: false
title: Quick Post - PowerCLI Firewall Requirements
type: post
url: /2018/quick-post-powercli-firewall-requirements/
categories:
- '2018'
- September
tags:
- As Built Report
- firewall
- powercli
- powershell
- VMware
- VUM
---

Last night I was working in an environment that has a reasonably well locked down NSX distributed firewall, and I was having some issues with PowerCLI. PowerCLI 10.2 on Windows, connecting to vCenter Server 6.7, to be specific. Port 443 was allowed from the jump host to vCenter Server, but I was seeing some odd issues still.

I was trying to run an [as built report](https://github.com/tpcarman/As-Built-Report) for the environment, but I could not seem to establish a connection to NSX manager when running the script, and VUM cmdlets that are called in the script were also failing, such as:

```    
Get-PatchBaseline               Unable to connect to the remote server
```

This was the error message I was getting when the script was attempting to use the existing PowerCLI vCenter Connection to connect to NSX Manager, using Connect-NSXServer:

``` 
Using existing PowerCLI connection to 10.10.12.40
Caught an exception:
Exception Type: System.Management.Automation.RuntimeException
Exception Message: Connection to NSX server 10.10.12.41 failed : System.Net.WebException: The underlying connection was
closed: An unexpected error occurred on a send. ---> VMware.VimAutomation.Sdk.Interop.V1.ErrorHandling.UnhandledVimExcep
tion: Unexpected exception has occured ---> VMware.VimAutomation.Sdk.Types.V1.ErrorHandling.VimException.VimException: T
here were one or more problems with the Update Manager Server certificate:

* The certificate's CN name does not match the passed value.


    --- End of inner exception stack trace ---
    at System.Net.TlsStream.EndWrite(IAsyncResult asyncResult)
    at System.Net.ConnectStream.WriteHeadersCallback(IAsyncResult ar)
    --- End of inner exception stack trace ---
    at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
    at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()
```



Initially I couldn't figure out why the VUM cmdlets were failing, or why connecting to NSX Manager was problematic and complaining about the certificate's CN not matching the passed value. This vCenter had trusted certificates installed and I was using the FQDN, which existed in the CN, to connect to vCenter. I could bring up a new Powershell session and connect to both vCenter Server and NSX Manager directly with no issues at all.

I decided to open up the outgoing firewall from the jump host temporarily to see if that worked, and immediately after doing so both of the issues I had above were gone. I used netstat -a to check the connections from the jump host, and I saw two connections to the destination vCenter Server:

``` 
TCP 10.10.0.10:62405 vcenter:443 ESTABLISHED
TCP 10.10.0.10:62405 vcenter:8084 ESTABLISHED
```

So Powershell was establishing a connection with vCenter on both TCP ports 443 and 8084, with 8084 being the VUM SOAP server port. This also explained why I was seeing certificate issues, as the custom certificate installed on this vCenter Server is for the machine SSL cert only, which listens on port 443 only (to my understanding).

I couldn't really find much about the VUM PowerCLI module still needing connectivity to vCenter on TCP 8084, so I thought I'd whip this post up quickly. It wouldn't surprise me if VMware are already in planning to wrap this in the primary session over port 443 that PowerCLI establishes on connection with vCenter Server, but for now I've just added 8084 to the firewall rule and I'm back in business!
