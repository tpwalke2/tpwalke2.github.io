---
layout: post
title: Build Pipeline Decisions
date: '2023-02-07T07:00:00.000-05:00'
author: Tom
tags:
- continuous-integration
- continuous-delivery
---
For most of my professional career, I have had continuous integration (CI) pipelines available. CI has been so helpful
that I now consider it essential. Essential enough that I once stopped an interview process at a company where CI was
not in place. Without a CI pipeline, I do not see how you can be certain that your most recent changes are correct.

More recently, I have also had continuous deployment (CD) pipelines available. While the CI pipeline provides the
confidence that the changes meet the basic requirements in the automated tests, the CD pipeline provides the confidence
that the software works on machines other than just my development environment. CD pipelines remove the excuse that "it
worked on my machine".

There are a number of decisions to make before building a CI/CD pipeline:
- On-prem hosting? or 3rd-party service?
  - Do you want more control over the hardware? Or are you willing to trade control for ease of administration?
- If on-prem, custom pipeline software? or 3rd-party (e.g. Jenkins)?
  - Custom software allows more control, but at the cost of increased maintenance in the future.
- If 3rd-party service, which one (e.g. Azure DevOps, CircleCI, etc.)?
  - These services seem like commodities, however, you have to look at the details of each one to determine if they fit
    your situation.
- If 3rd-party service or software, do you write a custom script? Or use the configuration language from the service?
  - Using the proprietary configuration language provides an optimal usage of the 3rd-party service, but contributes to
    vendor lock-in.
  - As with the custom pipeline software, custom build scripts require maintenance as the software matures.

In the next installment, I will to walk through some of my decisions on a couple of recent projects.