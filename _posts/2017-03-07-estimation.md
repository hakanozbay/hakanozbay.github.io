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

Most importantly, this applies to you. You need to know yourself. The better you know your own abilities, the better your personal estimate will be. 


## Time and resources are not proportional ##

A common illusion is to assume that the time to complete a project can be perfectly divisible by the number of people working on it i.e. Time is driectly proportional to resources. This is not true.  

For example if we have 12 man months of work to complete, the assumption is that if there are 2 people it would take 6 months, with 3 people 4 months, with 4 people 3 months etc... The misconception here is that each person has an output efficiency rate of 100%. This assumes each person has perfect knowlegde of the domain; perfect knowledge of the skillset required; has only independent work and nothing overlapping with the other resources; does not require assistance, collaboration or consultation with other resources; there are no learning curves therefore anyone can join having immediate and full impact. This is obviously theoretical and not realistic at all.

However many resources we have, we must estbalish the overall ***effective resource rate***. Having 3 resources does not mean our effective resource rate is 3.0, it would be less. For example the effective resource rate could be 2.0, whereby each resurce utilizes 0.33 of their time for collaborating and consulting with each other or learning the required skills and the domain. Everyone's individual resource rate would then be 0.67, multiplied by 3 resources would result in the effective resource rate of 2.0. Therefore 12 man months of work with 3 people working at an effective resource rate of 2.0 would result in a completion time of 6 months. 

When establishing the effective resource rate, you will need to consider the environmental and circumstantial factors mentioned above which are natural deviations for any resource from achiving full output efficiency. 

The overall effective resource rate is also not proportional to the number of resources. There is a peak effective resource rate value, which essentially represents the optimum number of resources. Adding more resources will cause the effective resource rate value to decrease leading to dimishing returns and making the completion time longer. Infinite resources do not infinitely improve the effective resource rate. You should establish what the optimum effective resource rate would be and therefore know what the optimum number of resources would be to achieve that rate.


## Your estimates are theoretical ##

[talk on estimation]: https://skillsmatter.com/skillscasts/8557-estimation-what-when-why-by-robert-martin