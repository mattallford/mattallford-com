---
author: Matt Allford
date: 2017-05-11 22:36:08+00:00
draft: false
title: Melbourne VMUG UserCon 2017
type: post
url: /2017/melbourne-vmug-usercon-2017/
categories:
- '2017'
- May
tags:
- community
- melbourne
- usercon
- vmug
- vmwhere
---

Back in March, I attended the [Melbourne VMUG UserCon](https://www.vmug.com/Attend/VMUG-UserCon/Melbourne-VMUG-UserCon-2017). This is the first UserCon I attended, and damn was I impressed! From what I've heard via peers, VMUG hasn't been having the best run lately, world wide. Needless to say, the team behind both the Sydney and the Melbourne events did a fantastic job. This year consisted of a rockstar lineup of speakers not only from VMware, but also from partners and affiliates who presented fantastic breakout sessions during the day. As usual, [Alastair ](https://twitter.com/DemitasseNZ)and [Brett ](https://twitter.com/BrettJohnson008)were in full swing with the vBrownBag recordings at the event, unfortunately I left it too late to be able to submit a small talk for vBrownBag.

I flew over from Tasmania on the first flight, and a quick Skybus ride into the city had me walking down to Crown Promenade not long after 8:00am. As I was walking down with my earphones in, around 500 metres from the event, I looked up and around 30 metres in front of me were the backs of heads of two familiar faces - [Duncan Epping](https://twitter.com/DuncanYB) and [William Lam](https://twitter.com/lamw). They probably thought I was some crazy local (because they are in Melbourne, right?), as I caught up and thought I'd take the opportunity to introduce myself and walk the remainder of the trek to the conference facility with them. Needless to say they were nice enough to chat on the way in.

After finding the right hall and sorting out the registration, I floated around the main section of the event catching up with colleagues and industry peers, some who I've only met once or twice but chat regularly online with. It's always great to catch up in person and drop some face-to-face banter as well.

I jumped in to the Veeam session, presented by [Anthony Spiteri](https://twitter.com/anthonyspiteri) who works for Veeam and is based out of Perth, WA. It was a great session with a quick look into some Veeam features including vSAN/VVOL/SPBM integration / awareness, as well as an overview of the recently announced Windows and Linux Agents.

There was a great panel session, with none other than [Alan Renouf](https://twitter.com/alanrenouf), William Lam, [Emad Younis](https://twitter.com/emad_younis), [Amy Lewis](https://twitter.com/CommsNinja) and Duncan Epping in the hot seat, hosted by [Greg Mulholland](https://twitter.com/g_mulholland). This was a 45 - 60 minute chat about anything and everything, from career advice, discussions regarding mentors, blogging platforms, and a good bit of banter about the [#1 top blogger spot](http://vsphere-land.com/news/announcing-the-top-vblog-2016-results.html).

[![](/wp-content/uploads/2017/05/Mel_VMUG_Panel-1024x768.jpg)
](/wp-content/uploads/2017/05/Mel_VMUG_Panel.jpg)

Next up was Runecast, presented by [Stan ](https://twitter.com/sferk)and my colleague [Nick Bowie](https://twitter.com/NickBowieNZ). Personally I was pretty new to Runecast. I knew the product from a conceptual standpoint, but I'd not had time to deploy the product and have a good look at the capabilities. Stan gave a good walkthrough of the history of the product and the reason for it existing, and provided a couple of great actual examples where Runecast had solved issues before they caused major issues, which was only a matter of time. Nick finished off the session with a real customer example, who had some large linux virtual machines that weren't performing quite as well as their physical counterparts. Runecast was deployed into their environment, not particularly for this reason, and on first scan an interesting KB was detected in regards to poor TCP performance on Linux machines with LRO enabled. A quick tweak to the machines, and I believe their performance was then as good as, if not better, than the equivalent physical machines. I've since had time to look at the product properly and have [put up a post](http://virtualtassie.com/2017/runecast-analyser-review-and-walkthrough/) that may be of interest.

[Claire O'Dwyer's](https://twitter.com/CloudyODwyer) session was next, who provided the audience with a really interesting perspective on recruitment, from an experience recruiter's perspective with a heavy focus on VMware. I've not dealt much with recruiters, but I've heard a lot of stories. It was refreshing to hear Claire's understanding of the products, the relative positions and even down to the certification stack. The session had some great takeaways for personal development and the audience had some great questions, none of which tripped up Claire. Great job!

Last up was the big one. The closing keynote presented by William Lam and Alan Renouf, and of course the focus of the session was on automation. The crux of the session was that the guys deployed a SDDC vSphere environment on an ESXi host ([Which William has since posted a 3 part blog series on](http://www.virtuallyghetto.com/2017/04/project-usb-to-sddc-part-1.html)), a supermicro E200-8D in this instance, simply by plugging in a pre-prepared USB drive and powering on the machine. The deployment took around 45 minutes, which was a bit quicker than the Sydney presentation using an Intel NUC. While we all waited anxiously (probably not quite as much as the guys on stage), Alan and William took us through the fictitious scenario and problem they were solving with this automation example. William took us through the nuts and bolts as to how the idea was born, put together and how the process was working end-to-end. Alan reminded us that it's OK to <del>steal</del> borrow code that has been shared by others. At the end of the day, this is a fantastic community, and the creators are putting the code out there on blog posts or GitHub in hope of being able to help someone else, so why reinvent the wheel if someone has done the hard work and is prepared to share it. He also informed us that when we feel the need to automate something, to not dive straight in to your favourite editor and try and bolt things together. Take a step back, and write down the manual process of how the task would be completed. From there you can break down each components and tackle small sections of the task at a time, and put it all together at the end.

Towards the end of the session, but not before being served a well earned beer on stage by Duncan (see below), vCenter booted up on the ESXi host and the guys took us through some of the REST features that have been included in the 6.5 version of the product.

I've waffled on a bit, but in summary a fantastic event. Well organised, awesome lineup (thanks to everyone for travelling from afar), great sponsors to make it happen and it was great to catch up with mates and meet a few new people. I was also lucky enough to clean up with a few prizes as well, and finished off the day with a couple of beers down on the water front. I can't wait to do it all again next year.

{{< tweet 844792238978719745 >}}


