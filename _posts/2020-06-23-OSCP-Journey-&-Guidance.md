---
layout: post
title: OSCP Journey & Guidance
author: Rajat Rao
date: '2020-06-23 14:35:23 +0530'
category:
        - OSCP
summary: Beginners guide to mobile security (Android).
thumbnail: /assets/img/posts/OSCP.png
---

**Offensive Security Certified Professional (OSCP)** is a foundational course of **Offensive Security**. This post is for the target audience who are unsure about enrolling for OSCP certfication, guidance for OSCP preparation, a dedicated schedule to prerpare for OSCP (Yes! even for newbies)	

Speaking about my OSCP journey, I received my OSCP certificate in November 2019 and I was able to clear it in my second attempt. A lot of miscalculaltions,  improper time management, inadequate pentesting environment lead me to failure as well as success. Not only OSCP helped me honing my Pentesting Skills but also attributes such time-management, never-give up attitude and most importantly, PATIENCE. 

A person planning to enroll OSCP needs to have a lot of patience as at times (ALMOST EVERY TIME!), you get stuck, confused and reach a dead-end while practicing machines. Just like in school/college days, when get stuck in solving mathematical problem and the only person to look for are our professors (Kidding, your best friend always has the solution;P). My role would be something similar like a professor/friend to help you schedule and prepare for OSCP certification.

Enough about my part, getting to the main part i.e OSCP Guidance and Schedule. But before that, let us cover few introductory sections.

`1)Certification Process`: Virtual labs are assigned to you once you have bought the labs package to practice your skills and once the period is completed, you are asked to set a date for examination. The OSCP exam is 24-hour time limit and consists of a hands-on penetration test in Offensive Security's isolated VPN network with a procture monitoring your work throughout the examination. You'll receive the connection pages few minutes prior the examination which you have no prior knowledge or exposure. Post the exam, 24-hour time is alloted to for creaating and submitting the report. 

`2)Labs`: Virtual labs are a dedicated lab consisting of a network of machines where one can practice/hone their pentestig skills. One can access these labs with the help VPN connectivity provided once you have paid for labs and time-frame of the labs depends upon the package you have dediced.

&nbsp;

Below is my 4 months journey of OSCP preparation. I have divided into 3 parts as follows:

* [Pre-Enrollment Phase]({{site.url}})

* [Post-Enrollment Phase]()

* [Buffer Phase]()

<p id="Pre-Enrollment-Phase">&nbsp;</p>

## Pre-Enrollment Phase

Prior enrolling for OSCP, I had decided to clear out my the very basic of Pentesting. In order to acheive that, I focused on the following things

- **Linux Basics (Temrinal command usage)**: One needs to have a good hands on experience on Linux operating system. To get familiar with the commands and working of Kali linux, I practiced challenges provided over [Bandit Overthewire](https://overthewire.org/wargames/bandit/). There are a total of `34 levels` and I set a deadline of 15 days to finish it off. This will not only help you to make you familiar with terminal commands but also helps one to learn security topics.

- **Basics of CTF**: Once done with the above, I decided to understand the structure of CTF and how different scenarios and exploit environment are developed for CTF players. This step will make you understand the CTF environment. I was able to get a list of machines from my colleague [Pratik Jadhav](). Following is the list of machines I solved before enrolling for OSCP:

### List

1. hackfest series -> hackfest2016 : Quaoar,hackfest2016 : Sedna and hackfest2016 : Orcus
2. Kioptrix Series
3. SkyDog Series: Skydog, SkyDog 2016
4. billu box
5. sickos series 1 and 2
6. Fristileak 
7. droopy 
8. Milnet 
9. Necromance 
10. Troll 1 
11. Acid Series: Server, Reloaded 
12. Lord of the root
13. Sokar and Pwnlab machines
14. Darknet
15. DonkyDocker 
16. Mr ROBOT -> Easy to Medium
17. Minotaur - > Medium
18. pipe machine 
19. Zico machine. Great Machine - Easy to medium
20. LazySysAdmin to
21. covfefe - Very Easy machine.
22. Breach SeriesBreach 1 and Breach 2 and Breach 3

Out of the above, I was able to understand and solve 15 machines. 

The entire step took around 15-20 days to concluded.

&nbsp; 
<p id="Post-Enrollment-Phase">&nbsp;</p>

## Post-Enrollment Phase

Once you have thoroughly followed the steps in Pre-Enrollment Phase, I can assure you are ready to enroll for OSCP labs. I followed the above and purchassed OSCP labs (60 days). 

On purchasing the labs, you get a buffer of a week or so before the lab access gets started. As walkthroughs of machines mentioned in Pre-Enrollment Phase were available online, to test my pentesting knowledge, I started solving [HackTheBox](https://www.hackthebox.eu/) machines. I was able to solve 2-3 easy machines during the buffer period (Without any hint). 

Once the labs access is started, you get study materials with respect to OSCP (pdf and videos). I highly recommend you'all to finish the pdf/videos as soon as possible. I mostly finished the study material during my fillers time. For instance, I read the study material while tarveling, lunch, coffee breaks and more. This not only helped me to understand the core of approach but strengthen the basic.

In the 60 days of access, I was able to solve around 35 machines. In the given period of time, I would ask to ones appearing for OSCP to solve atleast 30 machines.



&nbsp; 
<p id="Buffer-Phase">&nbsp;</p>

## Buffer Phase

Once the Lab access is over, make sure you set an exam date 14 days post the lab access get expired. Exam date can be selected anytime once the lab access is provided. 

As mentioned above, I got a buffer of 14 days post lab access expired. During this phase, the very first I practiced `Buffer OverFlow`. There are lot of online open sources tools where one can practice `Buffer OverFlow`. 

Following is the list of software to practive Buffer OverFlow
- Slmail
- Floatftp
- Pcftp
- Minishare

Concept of Buffer OverFlow with good hands-on will take 3-4 days. 

Post Buffer Overflow, we can again finish off the remainder machines which were listed in the `Pre-Enrollment Phase`. Along with that we can make notes of all possible methods/steps discovered by you while practing lab machines.

&nbsp;

## Summary

<table style="width:100%">
  <tr>
    <th><b>Phase</b></th>
    <th><b>Topic</b></th> 
    <th><b>Timeline (Days)</b></th> 

  </tr>
  <tr>
    <td rowspan="2">Pre-Enrollment</td>
    <td>Linux Basics</td>
    <td>15 <br>
    </td>
  </tr>
  <tr>
    <td>Basics of CTF</td>
    <td>15-20 (17 avg)</td>
  </tr>
  <tr>
    <td rowspan="3">Post-Enrollment</td>
    <td>HackTheBox Machines (2-3 easy machines)</td>
    <td>7</td>
  </tr>
  <tr>
    <td>Lab notes (full) + Lab Machines</td>
    <td>7</td>
  </tr>
  <tr>
    <td>Lab Machines</td>
    <td>53 (60-7)</td>
  </tr>
  <tr>
    <td rowspan="3">Pre-Examination</td>
    <td>Buffer Overflow</td>
    <td>4</td>
  </tr>
  <tr>
    <td>Remainder Machines</td>
    <td>7</td>
  </tr>
  <tr>
    <td>Notes + Revision + Attacking Machine Through Setup</td>
    <td>3</td>
  </tr>
</table>





