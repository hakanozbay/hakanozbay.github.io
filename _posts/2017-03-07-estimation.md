---
layout: post
title:  "Estimation: Making realistic estimates"
date:   2017-03-07 14:41:09
published:	false
---

Estimates for completion and delivery of software components or entire projects tend to fail generally. Estimates are continually revised and refined over time, infinitely extending the completion date until it is done. Why is this and what can be done about it?

The [talk on estimation][] by Robert Martin provides good insight, direction and tactics for improving estimates. This talk along with my own personal experiences at work has helped me formalise my own approach for estimation.

## What are we estimating? ##

This may seem obvious at first, but is not straight forward. Estimates for completion of a software component is generally treated as only the length of time it takes for the actual software development to complete. This is only a small part of the bigger picture. It is important to think about *what an estimate should actually involve*.

The criteria for estimates should involve:

- **Analysis** (gathering requirements)
- **Designing** (architecture, prototyping)
- **Developing**
- **Testing** (test suites, QA, UAT, bug fixing, amendments)
- **Promoting to Production** (attain sign offs, internal admin requirements, red tape)

You may also need to consider documentation or training aswell in your estimates depending on your software or company policy.

If you are making estimates for a larger project (i.e. several software components) then the same criteria for estimates should be applied for all components. The sum of the estimates for each component indicates the work days (also known as man-weeks, man-months) it will take for completion.

## Know your resources ##

By resources I mean people. If you are delegating particular software components to other team members, you should factor in their skills, aptitude and capabilities when estimating how long each part of the criterias above will take for them. Knowing them better will help make a better estimate, as the estimate is tailored for them, than just what you expect from them. Of course you should consult your team members to provide you estimates aswell, but it should encompass the criterias as part of it.

Most importantly, this applies to you. You need to know yourself. The better you know your own abilities, the better your estimate will be. 


## Time and resources are not proportional ##


perfect knowlegde of the domain, perfect knowledge of the skillset required, no overlapping work, independent work

infinite resources do not make it better. find the optimum number. 

## Your estimates are theoretical ##

[talk on estimation]: https://skillsmatter.com/skillscasts/8557-estimation-what-when-why-by-robert-martin