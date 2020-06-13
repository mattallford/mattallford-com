---
author: Matt Allford
date: 2019-12-29 04:20:46+00:00
draft: false
title: Azure AZ-103 Certification Experience
type: post
url: /2019/azure-az-103-certification-experience/
categories:
- '2019'
- December
tags:
- az103
- azure
- certification
- exam
- learning
- microsoft
- study
---

# Introduction


Towards the end of 2019, I made a decision to aim for a certification in one of the primary public clouds. The decision on which cloud to focus on was relatively simple for me for a couple of reasons:



* Microsoft certianly seem to be doing awesome things with Azure and it feels like there is a lot of hype around Azure in the circles I travel in, at least more so than GCP or AWS
* I work for a partner and while not specifically in the team that are delivering public cloud solutions and managed services, we are currently 100% focused on Azure and are a Microsoft triple gold partner. It makes sense for me to skill up in the public cloud that the company I work for is fully invested in

I don't want this to become a discussion around whether certifications are "worth it", but specically on the second point above, when you work for a partner, almost all vendors still put requirements on partners to have 'x' amount of certified staff at different levels to achieve different partner statuses. The reality is, if you are a technical staff member working for a MSP / VAR, you should absolutely expect your employer will want to you become and remain certified with key partner vendors.

I also personally enjoy certifications because you need to maintain focus on a specific structured learning path, known as an exam blueprint, that the vendor wants you to follow to become certified in a particular technology or track.

After picking the public cloud I wanted to skill up on, it was time to take a look at the certifications available for Azure, which are summarised on [this page](https://www.microsoft.com/en-us/learning/azure-exams.aspx). I immediately gravitated towards the AZ-103 exam. The blueprint of the exam looked like it was going to give me a great look into the foundations of Microsoft Azure. Lock it in!


# Study Material


While I understand a lot of the concepts and have been able to talk about various public cloud offerings at a high level (usually in conceptual discussions extending from an on-premises deployment), actually consuming public cloud was new to me. It seems a bit odd to write and read that and the end of 2019, but it is the truth and my efforts over the past 5 years have been focused elsewhere. I knew for this certification it was going to take a fair bit of work for something I hadn't had any hands on experience with.

My preferred method of learning is video training and my current go-to for that is Pluralsight. After a quick search for AZ 103, I found they had a [learning path](https://app.pluralsight.com/paths/certificate/microsoft-azure-administrator-az-103) available which aimed to match up with the exam blueprint. I did also use A Cloud Guru, but I found their content skipped over some of the blueprint items and sometimes didn't go to the depth I felt was required, but at the time of writing I believe that course is still being updated and expanded on. I also looked at the AZ 103 course on Linux Academy, this was my least preferred out of the three and I didn't use this beyond the free trial period. The audio during the course was problematic and distracting, and for some reason the instructor kept an exam guide open during the sessions which meant effectively half of the screen real-estate was not able to be utilised, it felt kind of awkward.

Through work I also am provided with $210 per month in Azure Credits, which I pretty much used every month for 3 months while studying for this exam. You'll need hands on experience with Azure to complete AZ-103. If you don't have access to a partner credit sceme, you can sign up to Azure and receive $200 in free credits for a month.

I also often used the [official Microsoft Documentation](https://docs.microsoft.com/en-us/azure/#pivot=get-started&panel=get-started1) for Microsoft Azure. I've kind of been out of the Microsoft space for a good 4-5 years, and I can honestly say I was surprised at how awesome the documentation for Azure is. Go ahead and take a look for yourself, it is really consumable and well written.

The last thing I used, which I do for all exams I am preparing for, is an excel document to self-assess where I am at with my learning. I basically dump out the exam blueprint in to individual lines in Excel and then use a traffic light system of red/yellow/green to self-assess each objective. I went and built my own out, but then I found [this awesome one](https://build5nines.com/free-oss-exam-self-assessment-tool/) from build5nines.com that uses a similar concept but takes it a step further and provides % ratings for each section of the exam. I highly recommend grabbing a copy, it seems like they build one for all of the Azure exams (links on the page I linked to above). When I got above 50%, I felt like it was time to book the exam.


# Online Proctored Exam Experience


I live in Launceston, Tasmania (Australia). The closest test centre to me is around 170 kilometers away in Hobart, which is a city I rarely visit these days. The next closest is Melbourne, which is a 45 minute flight away and interestingly the door-to-door time when compared to going to Hobart is only around 15-20 minutes longer. Anyway, the point is, for me to sit an exam at a test centre, I need to schedule it for when I travel for work. This isn't always a bad thing as it often has forced me to get back on track with study, but it can be frustrating depending on my schedules and availability of the test centres themselves.

I was pretty keen to try out the Microsoft Online Exam using Pearson Vue's "[OnVue](https://home.pearsonvue.com/microsoft/onvue)" platform. Microsoft have a [pretty good page](https://www.microsoft.com/en-us/learning/online-exams.aspx) covering this exam method, so I won't really go in to much detail, but I will say that overall I was pretty impressed with the process. On the booking side of things, this was pretty standard using the Pearson Vue process but instead of selecting a test centre, you chose to do the online exam. The availbility was fantastic. It basically had every time slot available in 30 minute increments, 24 hours a day, every day from the day I booked my exam, which was on the 16th of December, 2019.

Come exam time, I cleaned everything off my desk, closed the door to my study and started the check in process. I was able to use my mobile phone to take photos of myself, my identification and 4 photos of the surroundings of the desk where I was taking the exam. I was then connected to a person who would monitor me via webcam during the exam. We quickly scanned my surroundings with the webcam on the laptop, showed that my mobile phone was out of reach and I had nothing written on my arms and then I was launched in to the exam.

Microsoft providing the ability to sit their exams online is not a new offering, I found some posts going back to 2014 covering online proctored exams. I enjoyed doing the exam from the comfort of my own home and I thought the whole process end-to-end was exceptional. I hope other vendors will look to offer this method as well (looking at you, VMware!).


# The Exam


Even though it was a week ago, there's not a whole lot about the exam itself that I actually remember! The interface was good, I think I had 2 or 3 "scenario" based questions where you are provided with a fair amount of detail regarding a particular scenario and then are asked 3 or 4 questions specifically about the scenario. After reading about some other exam experiences, I was expecting a question or two in a lab where you had to go and do something, but I didn't receive any lab based questions at all. From memory there were 46 questions total. I finished my exam in around 45-50 minutes and then spent a couple of minutes doing a quick review of 3 questions.

I thought all of the questions were fair. A few caught me by surprise and I thought they were a bit obscure, but this is the nature of an exam of this format. I enjoyed the fact that not every question was a multiple choice. Some were asking you to drag and drop steps or actions in specific orders to achieve an outcome, or provide information on missing components of powershell commands or ARM templates.

After clicking the "finish" button, I was glad to see I scraped in with a score of 760 (700 is a pass). Because I did the online exam, I was then taken to a summary screen that had my photo and score - something you'd normally receive as a print out in an exam centre. I didn't want to open up any screenshot tools and risk getting disqualified, so I simply clicked on the next button and then I was taken back to my desktop image. I didn't receive an email with the score or summary, but in the Microsoft Learn portal after a short time the achievement did show up in there.



I was really glad to get this exam done before the end of the year. It meant that I can take 3 weeks off work and have some much needed down time and also move on to the next technology I want to learn and get hands on with. I can absolutely see myself aiming for another public cloud certification sometime in 2020. I don't yet have any formal ceritifcations in my plan for the next 12 months, that's something I'll likely aim to draft up over the next couple of weeks before I get back to work. There is so much I want to do but only so much time in each day, so some prioritisation will absolutely be required.
