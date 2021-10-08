---
title: "Canonical Landscape Review"
date: 2021-10-07T16:49:17+01:00
tags: [ubuntu,linux,saas]
draft: false
---

For the past 2 years I’ve relied on Canonical Landscape extensively. In this post I’ll summarize my experience with the platform as well as share some important lessons I learned for anyone running mission-critical applications in which Landscape plays a part of the workflow.

For anyone that isn’t familiar with the software, Landscape is Canonical’s SaaS offer to maintain a fleet of Ubuntu servers. From keeping track of USNs, package and user status, to performance and overall maintenance of the servers. Landscape allows, through the usage of groups and tags, a single Landscape account to handle and maintain different deployments with different requirements and characteristics. 

Landscape is a foundational tool for Ubuntu servers. That is, it works at a level where functionality and security become hardcore requirements. While there are other tools for orchestration of GNU/Linux servers, Landscape is something else. It doesn’t focus solely on orchestration and instead provides several interesting features that go beyond automation or easing repetitive tasks.

It’s important to mention that Landscape, while an important tool for anyone managing Ubuntu servers at scale, isn’t trying to be a world changing SaaS application. The platform was built, along its client, to perform - while important - basic tasks on an Ubuntu server. And it’s precisely because of this point that I’ll be more demanding in my review. 

Having used the platform for >2 years I can safely say that Landscape does not rank high in Canonical’s priority list. 

There are many problems with the platform ranging from performance and usability to complete lack of support from Canonical. That being said, what concerns me the most about Landscape isn’t the problems that are evident, but the ones that are not. 

Due to the nature of what this platform does, and the fact that Canonical is not losing any sleep about its poor state, I consider the usage of Landscape to be a security and continuity risk.

I’ve spent several hours with their engineers *(while this was still possible)* working on figuring out out-of-sync issues between the Landscape API and the landscape-client. In some cases the problems were so evident that I felt like no one else was reporting them because no one else was using the platform.

In my experience with Landscape I, exhaustly, experienced:

- Clients running out of sync *(this will leave your client unmanageable)*
- There’s no one *(no link/email)* for support
- It’s SLOW! Every now and then they’ll work on performance but it’s only a matter of weeks until it’s back to its sluggish state

Seeing as the goal of the platform is to serve as the backbone of administration and monitoring, there’s nothing worse than creating a dependency on something that will so clearly, and often, fail you.

I’m genuinely concerned about the dependency that I’m creating on Canonical. Judging by how little interest they’ve shown in the platform, I wouldn’t be surprised if Canonical pulls a Google on us and shuts the service down. Or worse, my fears for its security come to fruition.

While I’m sure my use case isn’t unique, it is important to mention that in some scenarios, and by design, I’ve trusted Landscape as my only means of access and visibility to hosts. That is, after certain systems are deployed, I either run them through Landscape or, in the event *(read: when)* it fails, I’ll lose my access to production servers.

It’s quite unfortunate that the platform is going down this path. Canonical has the upper hand by being able to integrate natively with their OS and make use of a lightweight client. Unfortunately, they don’t seem interested in doing that. It got to the point where paying customers of the platform do not have support. That is, you can’t report problems with the platform because there is no one to report them to. Ironically, there is support for billing-related questions *(that part doesn’t seem to fail - go figure)*.

All of this leaves the question: What’s the alternative? Often people will mention Chef and Ansible, but those are not viable alternatives to Landscape. As far as I’m aware there isn’t a real competitor to Landscape, which might explain why Canonical doesn’t feel pressured into fixing a lot of its problems or usability issues *(or at a bare minimum, have support for a paid product)*.

Overall, I don’t recommend the usage of Landscape for any mission-critical systems. The lack of interest shown by Canonical is not only frustrating but concerning due to the security requirements of a platform such as this one.
