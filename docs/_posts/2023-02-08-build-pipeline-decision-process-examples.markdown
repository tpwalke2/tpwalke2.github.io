---
layout: post
title: 'Build Pipeline Decision Process : Examples'
date: '2023-02-08T07:00:00.000-05:00'
author: Tom
tags:
- appveyor
- circleci
- continuous-integration
- continuous-delivery
---
In the [last post][last-post], I laid out the decisions that go into a build pipeline. With this post, I want to walk
through my decisions on a couple of personal projects.

# CodePointEnumGenerator
The [code point generator][code-point] source generator is in a public repository. I wanted the continuous integration
(CI) part to compile the code and run the unit tests. I also wanted the continuous deployment (CD) part to deploy just
the main branch. So the pipeline also needed to selectively create a NuGet package, create a GitHub release, and publish
to NuGet.

The first decision I made was regarding the structure of the pipeline code. I wanted to keep open the option for
switching between services, so instead of using the proprietary configuration language of one or other services, I
created a [Cake][cake] script that performed each of the steps.

I wanted to work with a third-party service to get some experience with other options, so I decided not to go with
on-prem or custom pipeline management software. This may sound like a contradiction with the previous paragraph, but
notice that the pipeline code that I wrote lives in the repository. The service that I chose needed to handle the
responsibility of cloning the repository and running the pipeline code. These responsibilities can be custom designed,
but I decided against this.

Another concern is that I do not have a budget for build pipeline services, so there needs to be a free ($) tier
available. Since this project is [open-source][code-point-license] and in a public repository, I chose
[AppVeyor][appveyor], who offer a free tier for public repositories. I still had to write an AppVeyor specific adapter
to run the Cake script, but the proprietary configuration is minimal for that use case.

# Private Project (Name TBD)
I am working on another project that is not yet ready for the light of day. It is in a private repository.

As above, I wanted to keep open the option of switching between services (which came in handy), so I used the same model
of a [Cake][cake] script that performed the pipeline steps instead of a proprietary configuration and a proprietary
configuration adapter.

Because this project is in a private repository, [AppVeyor][appveyor] is not an option. I chose [CircleCi][circleci]
because they offer a limited free ($) option for private repositories.

In the [next installment][next-post], I will go over a recent period where I was able to test the portability of the pipeline code.

[last-post]: {% post_url 2023-02-07-build-pipeline-decision-process %}
[code-point]: {% post_url 2023-01-04-font-codepoint-source-generator-package %}
[cake]: https://cakebuild.net/
[code-point-license]: https://github.com/tpwalke2/CodePointEnumGenerator/blob/main/LICENSE
[appveyor]: https://www.appveyor.com/
[circleci]: https://circleci.com/
[next-post]: {% post_url 2023-02-09-changing-build-pipeline-providers %}