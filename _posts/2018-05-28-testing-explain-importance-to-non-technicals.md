---
layout: post
title:  "Testing: Explaining its importance to non technical people"
date:   2018-05-28 21:52:03
---

Do you ever find yourself trying to explain testing, its significance and consequences for not doing it properly to non technical people such as users of your software or your management team? Do they only care about meeting the deadline date? Are you unable to express this effectively for them to understand? Are you then left with broken software once released to production? I have experienced all of these. How do you get through to them? This post is to convey what testing involves, the consequences and impact for overlooking this. Direct these people to this post to help you!

There is a trade off in software development between **building the right system** and **building the system right.** Most teams focus on the former where it’s all about delivering features of software for users. The latter is about addressing the vulnerabilities of your system, such as performance, error handling, security flaws, scalability and efficiency of resource consumption. Unfortunately, lack of focus on this side of development leads to many problems in systems.

One of the main ways to identify errors in software is through testing. If we don’t test, the risks are: Operational, Financial and Reputational. Operational means the software will not work. Financial means as a result of the software not operating your company may lose money. Reputational means that you, your team or your company may lose the trust and respect of users and clients. 

There is an **inversely proportional relationship between test and risk**: no testing means high risk, lots of testing means low risk.

**Testing is an art – your software is only as good as your tests**. It’s not about the quantity of tests, it’s about quality, so you need to do the right kind of tests, covering all potential scenarios to ensure risks are minimized and resiliency exists. Tests come in the form of: unit, functional, integration, behaviour, simulation, acceptance, environment, performance, regression, stress… along with more different types, the list is extensive. Historically, only functional testing in isolation has been carried out which is not sufficient for software on systems covering large ground, multiple scenarios and those that interact with each other.

The best types of tests simulate production as close as possible over multiple iterations, as well as explore all scenarios/paths of code. This means identical data input, workflows, volumes, scheduling, interaction with other processes in the environment and creating error scenarios to assess error handling/recoverability. This ensures continuity when the transition to production occurs, so that it is seamless. 

This ideology behind ‘cutting corners’ for testing to save costs or meeting a release deadline is not synonymous to simulation or sufficient testing. You are jumping over potential pot holes that will be uncovered when run in production, which can be fatal. Here is an example of what I mean:

If you do not do sufficient testing in order to save $ x or to meet a release deadline - your operational, financial and reputational risks can be potentially realized. If these are realized, what will happen? Aside from the software crashing, It will create rippling effects throughout:
 
- Potential application outage – operational risk
- Dependent consumers application will not be able to continue their processing – cascading operational risk
- Clients won’t be able to utilize your services – financial risk
- Client’s trust will diminish – reputational and financial risk
- The team will be shamed for insufficient testing, and trust in competency will diminish – reputational and financial risk for the organization and future budget funding
- Incident review teams can be involved, consuming productivity and a lot of time of many people – financial and reputational risk
- Working under stressed conditions to rectify the issue (or even to meet the deadline in question for initial release) – operational and reputational risk (as well as psychological stress)

**If you monetize and sum these outcomes, you will see that they are magnitudes more than $ x and does not justify meeting a release deadline for.** The initial aim of doing this to satisfy and put a smile on the faces of dependants have greatly backfired, so much so that you are in a worse position than what you would be if you spent $ x or pushed back the release date.

Having experienced the above, I prioritize building the system right by developing test suites to ensure the software is at a high quality and at the right standard, _in addition_ to building the right system. **This is where we deliver true sustainable business value.** The type of test suites I develop are: unit, functional, integration, behaviour, workflow, acceptance and performance. The software I then release to production does not have defects, having achieved a high level of quaility assurance while testing. It will take time initially to build this foundation, but once this foundation has been put in place, future cycles for making changes to extend the software will be quicker and the software will still maintain its quality and operational stability in production. Release deadlines are met more accurately and continual delivery without regression keeps our business working seamlessly and keeps our users and clients happy. 

If you want to achieve Operational, Financial and Reputational stability, progression and continuity, have stable software, a stress free work environment, not firefighting software defects constantly and to inspire confidence in releases to your clients, then you must take software testing seriously and prioritize it as a critical criterion for success. There is no alternative to this. In my experience, views on the topic of testing reveal 2 types of people: Those who see reality, and those who will be hit by reality.
