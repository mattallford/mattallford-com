---
author: Matt Allford
date: 2016-11-30 05:08:31+00:00
draft: false
title: VCAP6-DCV Deploy - It's a Pass!
type: post
url: /2016/vcap6-dcv-deploy-its-a-pass/
categories:
- '2016'
- November
tags:
- Cert
- certification
- DCV
- deploy
- exam
- pass
- vcap
- VMware
---

Last week I sat the VMware Certified Advanced Professional - Data Center Virtualisation Deploy ( VCAP6-DCV Deploy) exam in Auckland and I'm happy to say I passed the exam. I thought I would do a bit of a write up below of my experience.

I've been reasonably heavily focused in the VMware space for about 2 years now. Prior to that I was a stock standard Windows Sys admin. 2 years ago I was working for a University and I ended up in a role change that saw me take on a bit more in the VMware and storage space, but nothing too advanced. I've never had any industry certifications and in 2016 I made it a bit of a personal goal to chase a few down. Towards the middle of the year I decided to pursue the VCP6-DCV certification. Because I was new to the certification track, I had to do the foundations and VCP exams. I passed both in June. From there I was looking at doing a Microsoft Exchange / Office365 certification because I worked a lot in this space at the University, but July was when I changed jobs and my focus shifted.

I now work as a virtualisation and storage engineer for a partner based out of Auckland, New Zealand. In October I sat down to think about what the next step for me is with professional development and certification. I decided to give the VCAP6-DCV Deploy some proper attention and make that my next target.

There are 8 major sections in the blueprint and my original plan was to try and tackle one of these per week, and then have a 1-2 week period at the end to go over it all as a recap. I'd recently started the new job and we had a new baby (12 weeks old at the time I sat the exam), so from a time commitment perspective this was going to be a bit challenging. As it happened, I ended up I sat the exam about 4 weeks sooner than I originally planned as well, more on that in a bit.


## Preparation


Diving into the VCAP, I started with the blueprint and came across [David Quinney's post](http://www.cloudypolitics.com/vcap6-deployment-study-guide/) where he had created an excel document of all of the blueprint objectives, with the plan to self asses knowledge and understanding of each topic. There were a couple of sections missing but as the objectives might change, it's a good idea to cross check if you plan to do this. I opted to use a traffic light system. Red meant I might know what the feature is but no idea how it really works or how to configure it. Yellow was a good understanding but found I still needed to brush up to be flawless. Green meant I was very comfortable and could easily do the task and fully understood the nuts and bolts. I ended up going into the exam with about 70% green, 25% yellow and a couple of items on red (hello resource pools).

I primarily used a [study guide](https://ucconnect.wordpress.com/2016/08/21/vcap6-dcv-deployment-study-guide/) from Ramy Mahmoud and also a couple of the Pluralsight videos to break up the learning type. I did also give a couple of the VMware hands on labs a go when I got a bit bored of reading / watching videos.

There is also a newly released [study guide](http://www.vjenner.com/vcap6-dcv-deployment-study-guide/) from Kyle Jenner, as well as a [simulator](http://virtualg.uk/vcap6-dcv-deploy-exam-simulator-free/) that's been kindly made available by Graham ([@virtualG_UK](https://twitter.com/VirtualG_UK)). I didn't get a chance to try this as it was released just before I sat the exam.

I read about the experience others had with the exam, mostly with the BETA. It was good to read about the experience of others and see how they tackled it.

Make sure to check out the following post from Josh Andrews - http://sostechblog.com/2016/06/15/vcap6-dcv-deploy-public-beta-launched/

Some of the key things are to remember:
* The exam is 190 minutes long and contains 27 questions
* Spend a few minutes to set up your display after the exam has started. Good instructions are on the first page of the on screen manual
* Control, ALT and Backspace keys are disabled. This caught me out a couple of times, probably wasted a few minutes at least with this
* Don't use the console sessions in the c# client or the VMRC in the web client. Because Control + ALT are disabled, and these both take mouse focus, you'll be in for some pain
* c# and web clients were both available

In my eyes a lab is a necessity for an exam like the VCAP deploy. I used a nested virtual lab and ended up with about 10-12 machines running VC/PSC/ESX/Storage/DC.

I'm based out of Tasmania (Australia) and there are no test centres here that allow you to sit the VCAP exams. So for me it was looking like either a day trip to Melbourne or to try and fit it in to one of my regular trips to Auckland to catch up with the rest of my work team. At the start of November I booked in a work trip to Auckland for the end of November. After thinking about it for a week or so, I decided I would give the exam a go when I was in Auckland, rather than then having to try and plan a day trip to Melbourne or Sydney. This meant I was going to sit the exam a few weeks sooner than originally anticipated, so I had to cram in a lot of study and prioritise what to focus on.

Instead of knowing the ins and outs and exact syntax of esxcli, I used the following a lot on the lab:


`esxcli esxcli command list | grep *item*`


If I knew roughly what I was looking for (storage, TCP, network, etc), then the above gave a decent filtered output that would quickly help me find what I'm looking for. I guess I'm comfortable with the Powershell discovery method of being able to find things rather than being able to remember things.


## Exam Day

My exam was booked in for 9:00am on a Thursday. I spent the Wednesday travelling from Launceston across to Auckland. I left Launceston on a 6:20am flight and arrived at the hotel in Auckland at about 5:30pm local time. It was a fairly long day, so I grabbed some tea and chilled out in the evening wanting to grab a decent sleep.

I got up early the next morning and had a good breakfast knowing I'd be sitting in front of a screen for 3 hours on an exam that is pressed for time.

I turned up to the test centre about an hour early - I guess I over estimated how busy the traffic might be. There was a coffee shop next door so I went there to get one last caffeine hit. I wandered into the test centre about 30 minutes before the scheduled start and did the usual things - signed the terms, had my photo taken and put all of my belongings in a locker. We weren't allowed to take water into the test room so I had a few last drinks, as I knew this exam was going to be a race against the clock.

I was sat down at the machine and the exam started. Overall I thought it was a really great exam, and I feel I am much more suited to the 'live lab' exams like the VCAP rather than the multi choice. I do still see the value in the VCP style exam though. I thought the questions were well worded and it was clear in each what was being asked of me. I'd spent a bit of study time working on some features and products that I hadn't really worked with before, and received no questions of them, but it was good to have spent the time to learn how they work and what value they might add to a design or customers vSphere deployment.

The lab worked well for me. It was reasonably responsive without much lag at all. I'd rate it pretty similar to actually doing one of the Hand on Lab sessions.

I'd have to agree with the general consensus that this exam is a race against the clock. If you don't keep an eye on the timer, it can be easy to let time get away from you. I thought I was doing OK, but if I had my time again there were some things I spent time on that I would have left and come back to. Especially when some tasks like rescanning can take a few minutes.

As posted by a number of people, the interface is similar to the VMware hands on labs. I'd recommend checking out the link I posted above for how to set up the interface. These steps are also listed at the start of the exam on the first page. Don't skip over them, quickly setting the interface will help in the long run.

As I went through the questions I noted down the question number on the whiteboard supplied and gave it a tick if I was happy, an **!** If I was pretty happy but wanted to recheck if there was time, and a **X** for those I skipped.

I ended up skipping 4 questions as they were topics I did not study and I knew my time was better spent trying to get the rest right. There were 3 or 4 I marked with a ! and the rest I was reasonably happy with. I'd finished a first run of all the questions with about 9 minutes remaining on the clock, so I was going back to the ones I marked with ! to make sure I got them correct. But when there were 7 minutes left according to the timer, my lab quit and a message came up to say time had run out. I checked with the employee at the training venue but she wasn't really sure. When there are 27 questions in 180 mins, it's about 6.5 minutes per question, so those extra 7 minutes would have been valuable to re check and fix the ones with ! After thinking about it, I think 3 of the 4 I marked with ! were either slightly incomplete or incorrect. I'm not sure if I actually had the correct amount of time in front of the screen and the timer was incorrect, or if it did actually end a couple of minutes early.

Anyhow, I decided to wait and see what happened. About 45 minutes after leaving the test venue I received an email with an attachment, letting me know that I had passed with a score of 385. Given I sat this a bit earlier than originally planned, I'm pretty happy with the result.
