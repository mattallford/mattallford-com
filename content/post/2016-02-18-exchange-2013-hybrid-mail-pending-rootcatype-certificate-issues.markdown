---
author: Matt Allford
date: 2016-02-18 21:40:27+00:00
draft: false
title: Exchange 2013 Hybrid Mail 'Pending' - RootCAType Certificate Issues
type: post
url: /2016/february/exchange-2013-hybrid-mail-pending-rootcatype-certificate-issues/
categories:
- February
tags:
- Certificate
- email
- exchange
- hybrid
- mail flow
- Office365
- rootca
---

I manage an Exchange 2013 deployment at work which is configured in Hybrid with Office365. Recently we had to change our SSL certificate that was being used for both TLS for the hybrid connection and also for our client facing DNS names. Due to changes with our 3rd party SSL certificate provider, this was a new SSL certificate installation rather than a renewal.

I generated the certificate and installed it onto all of the Exchange servers on-premises and during our change window, made the changes to bind the services to the new certificate and then ran the Hybrid connection wizard to update the certificate used in our On-Prem send connector to Office365 and also to the receive connector in our Office365 tenant. I'd confirmed the change was OK and sent some test emails back and forth over the hybrid connection, all looked good.

A few days later, a few tickets were being logged by our Service Desk team claiming that emails sent from our Office365 users to our On-Prem users was not being delivered. As usual I didn't have a huge amount of information, so I sent a test email from an account I have in our Office365 tenant to my mailbox which is on-prem, and then performed message tracing in powershell:
```powershell
PS C:\Scripts> Get-MessageTrace -SenderAddress Office365user@domain.com -RecipientAddress onpremuser@domain.com -StartDate (get-date).AddMinutes(-20) -EndDate (get-date) | fl


Message Trace ID  : d33e3c2f-5473-4b32-32ee-08d33855e852
Message ID        : <SIXPR01MB054236B5866583D8068F7D28D8AF0@SIXPR01MB0542.apcprd01.prod.exchangelabs.com>
Received          : 18/02/2016 11:23:21 AM
Sender Address    : Office365user@domain.com
Recipient Address : onpremuser@domain.com
From IP           : 
To IP             : 
Subject           : test email
Status            : Pending
Size              : 0
```

Sure enough, the email status is in a 'pending' state. I widened the traces a bit more to include some of the recipients that were claiming no mail had been received, and all emails had a status of Pending.

I did some searching, and there doesn't seem to be a whole lot of information regarding the pending status, and what it might imply. I knew the SSL Certificate change was the only change in our environment, so I started looking at this as being the source of the problem.

I ran the hybrid connectivity wizard again, picking the new SSL certificate in the last step of the process and it finished OK, but the emails were still pending in Office365.

Unfortunately our deployment has single role servers, so our CAS are split from MBX servers. I started looking at the SSL certificate config on the CAS machines where the receive connectors are. What I noticed was the **RootCAType** on our CAS machines was set to Registry, where as on our MBX servers this was set to **ThirdParty. **

```powershell
[PS] C:\Scripts>Get-ExchangeCertificate -Server excas1 -Thumbprint xxxxx | fl


AccessRules        : {System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule}
CertificateDomains : {owa.domain.com, autodiscover.domain.com}
HasPrivateKey      : True
IsSelfSigned       : False
Issuer             : CN=QuoVadis EV SSL ICA G1, O=QuoVadis Limited, C=BM
NotAfter           : 12/02/2018 1:53:00 PM
NotBefore          : 12/02/2016 1:43:23 PM
PublicKeySize      : 2048
RootCAType         : Registry
SerialNumber       : 172FF4C1A3B4D13282A17C8080C1E552475CD4CB
Services           : IMAP, POP, IIS, SMTP
Status             : Valid
```


I came across the following article which talks briefly to issues occurring if the certificate does not have a RootCAType of ThirdParty:

https://support.microsoft.com/en-us/kb/2879262

In our environment, the new certificate and the Root / intermediate were imported into the local machine certificate stores manually.

After some digging, I found that our Root CA Certificate was only located in the **Trusted Root Certification Authorities** machine certificate store. I went ahead and imported our Root CA Certificate into the **Third-Party Root Certification Authorities** store on the EXCAS servers and then checked the status of the Exchange Certificate again. It was now showing **ThirdParty.**

```powershell
[PS] C:\Scripts>Get-ExchangeCertificate -Server excas1 -Thumbprint xxxxx | fl


AccessRules        : {System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule,
                        System.Security.AccessControl.CryptoKeyAccessRule}
CertificateDomains : {owa.domain.com, autodiscover.domain.com}
HasPrivateKey      : True
IsSelfSigned       : False
Issuer             : CN=QuoVadis EV SSL ICA G1, O=QuoVadis Limited, C=BM
NotAfter           : 12/02/2018 1:53:00 PM
NotBefore          : 12/02/2016 1:43:23 PM
PublicKeySize      : 2048
RootCAType         : ThirdParty
SerialNumber       : 172FF4C1A3B4D13282A17C8080C1E552475CD4CB
Services           : IMAP, POP, IIS, SMTP
Status             : Valid
```

Without needing to do anything, email started flowing again over the hybrid connection. I performed some message tracing and could see all emails that had been in the status of pending and queueing were now being delivered successfully to on-premises mailboxes.

This might be one to watch out for if you push the Root CA certificate for your external CA out via Group Policy or something similar.
