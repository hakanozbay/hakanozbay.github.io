---
layout: post
title:  "Testing: Explaining its importance to non technical people"
date:   2018-05-20 21:22:00
published: false
---

There is a trade off in software development between **building the right system** and **building the system right.** Most teams focus on the former where it’s all about delivering features of software for users. The latter is about addressing the vulnerabilities of your system, such as performance, error handling, security flaws, scalability and efficiency of resource consumption. Unfortunately, lack of focus on this side of development leads to many problems in systems.

One of the main ways to identify errors in software is through testing. If we don’t test, the risks are: Operational, Financial and Reputational.

There is an **inversely proportional relationship between test and risk**: no testing means high risk, lots of testing means low risk.

**Testing is an art – your software is only as good as your tests**. It’s not about the quantity of tests, it’s about quality, so you need to do the right kind of tests, covering all potential scenarios to ensure risks are minimized and resiliency exists. Tests come in the form of: unit, functional, integration, behaviour/simulation, acceptance, environment, performance, regression, stress… along with more different types, the list is extensive. Historically, only functional testing in isolation has been carried out which is not sufficient for software on systems covering large ground, multiple scenarios and that interact with each other.

The best types of tests simulate production as close as possible over multiple iterations, as well as explore all scenarios/paths of code. This means identical data input, workflows, volumes, scheduling, interaction with other processes in the environment and creating error scenarios to assess error handling/recoverability. This ensures continuity when the transition to production occurs, so that it is seamless. 

This ideology behind ‘cutting corners’ for testing to save costs or meeting a release deadline is not synonymous to simulation or sufficient testing. You are jumping over potential pot holes that will be uncovered when run in production, which can be fatal. Here is an example of what I mean:

If you do not do sufficient testing in order to save $ x or to meet a release deadline, your operational, financial and reputational risks can be potentially realized. If these are realized, what will happen? Aside from the software crashing, It will create rippling effects throughout:

 
- Potential application outage – operational risk
- Dependent consumers application will not be able to continue their processing – cascading operational risk
- Clients won’t be able to utilize our services – financial risk
- Client’s trust will diminish – reputational and financial risk
- The team will receive a hazing for insufficient testing, and trust in competency will diminish – reputational and financial risk for the organization and future budget funding
- Problem Management Incident reviews will be involved, consuming productivity and a lot of time of many people – financial and reputational risk
- Working under stressed conditions to rectify the issue (or even to meet the deadline in question for initial release) – operational and reputational risk (as well as psychological stress)

**If you monetize and sum these outcomes, you will see that they are magnitudes more than $ x and does not justify meeting a release deadline for.** The initial aim of doing this to satisfy and put a smile on the faces of dependants have greatly backfired, so much so that you are in a worse position than what you would be if you spent $ x or pushed back the release date.

Having experienced the above before, I now prioritize in building the system right and develop test suites to ensure the software is at a high quality and at the right standard, _in addition_ to building the right system. This is where we deliver true sustainable business value. The type of tests suits I develop at the moment are:  unit, functional, integration, behaviour, workflow, acceptance, performance.