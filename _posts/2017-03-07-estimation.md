---
layout: post
title:  "Estimation: Making realistic estimates"
date:   2017-03-07 14:41:09
published:	false
---

Estimates for completion and delivery of software components or entire projects tend to fail generally. Estimates are continually revised and refined over time, infinitely extending the completion date until it is done. Why is this and what can be done about it?

The [talk on estimation][] by Robert Martin provides good insight, direction and tactics for improving estimates. This talk along with my own personal experiences at work has helped me formalise my own approach for estimation.

### What are we estimating? ###

This may seem obvious at first, but is not straight forward. Estimates for completion of a software component is generally treated as only the length of time it takes for the acual software development to complete. This is only a small part of the bigger picture. It is important to think about *what an estimate should actually involve*.

The criteria for estimates should involve:

- Analysis (gathering requirements)
- Designing (architecture, prototyping)
- Developing
- Testing (test suites, QA, UAT, bug fixing, amendments)
- Promoting to Production (attain sign offs, internal admin requirements, red tape)

You may also need to consider documentation or training aswell in your estimates depending on your software or company policy.

If you are making estimates for a larger project (i.e. several software components) then the same criteria for estimates should be applied for all components. The sum of the estimates for each component indicates the work days (also known as man-weeks, man-months) it will take for completion.

### Know your resources ###

### Time and resources are not proportional ###

### Your estimates are theoretical ###

[talk on estimation]: https://skillsmatter.com/skillscasts/8557-estimation-what-when-why-by-robert-martin