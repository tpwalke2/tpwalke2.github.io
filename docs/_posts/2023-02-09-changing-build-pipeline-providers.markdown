---
layout: post
title: 'Changing Build Pipeline Providers'
date: '2023-02-09T07:00:00.000-05:00'
author: Tom
tags:
- appveyor
- circleci
- jenkins
- raspberry-pi
- jetbrains-space
- continuous-integration
- continuous-delivery
- powershell
- bash
- docker
- msbuild
---
In the [last post][last-post], I walked through some of my decisions about the build pipelines in a couple of projects.
One of the key things is that I want the ability to easily switch pipeline providers in case the provider stops working
for my needs at some point in the future. This recently happened for the unnamed personal project I mentioned yesterday.

I actually wrote the [code point source generator][code-point] as a supporting utility for the other project. [Source
generators][source-generators] must target .NET Standard 2.0. So the SDK must be able to compile .NET Standard 2.0
libraries. The .NET 6.0 SDK *should* be able to compile it. However, I also keep NuGet packages up to date where
practical, and a recent update to some `Microsoft.CodeAnalysis` packages did not build with older versions of MSBuild.
The [AppVeyor][appveyor] build for the code point source generator had no problem continuing to build and publish the
project.

However, when I brought the source generator into my other project, the [CircleCI][circleci] build stopped working. The
only problem is that I did not immediately know that it was failing. I eventually checked on the built artifacts and
found that the last published one was from mid-December 2022, but there had been almost daily builds since then. I had
known that the pipeline did not seem to correctly report failures, but I had not prioritized that ticket because
learning a UI framework seemed more exciting. I saw again the importance of having the build pipeline correctly report
failures.

In the original pipeline, my adapter code for CircleCI used the [Windows orb][windows-orb] to run a Powershell script
that then ran the various `dotnet` command lines to run the cake file. The build was failing in the Cake file which
caused the `dotnet` command to fail, but CircleCI was still receiving an exit code of 0, indicating success. It turns
out that Powershell requires some extra work to publish [exit codes][powershell-exit-codes]. After I updated the
Powershell script, CircleCI started correctly reporting the failed builds.

Although the build was failing on CircleCI, when I ran the Powershell script locally, it successfully built.

I turned on verbose diagnostic messages and started comparing the traces from both systems. I saw a difference in the
MSBuild version. My local system has the .NET 7.0 SDK, but the Windows Orb had an older .NET 6.0 SDK. When I removed the
.NET 7.0 SDK from my local system and installed the same version of the .NET 6.0 SDK, I was able to replicate the failed
builds. Success! But now, why?

Again, it goes back to the source generator. I walked back the versions of `Microsoft.CodeAnalysis` packages, but I was
unable to get the project to build. I had to start looking at other options. The Windows Orb needed an update, but the
project owners did not have a planned update until later this month. So I looked at other build providers.

AppVeyor and TravisCI do not have a free ($) tier for private repositories. I do not mind paying, but when there is no
budget available, you work with what you have. Then I remembered [JetBrains Space][jetbrains-space]. I set up the
project in Space and wrote a configuration adapter to run the Cake script. Instead of a Windows Orb, the build used a
[Docker image][microsoft-docker] for the .NET SDK. And since the build was in Linux, instead of Powershell, I wrote a
Bash script to run the `dotnet` commands and start the Cake file.

The process of switching to Space was relatively painless, and now I have bootstrapping scripts for both Bash and
Powershell.

However, in Space, the build pipeline got past the build and MSBuild publish steps using a more recent version of the
.NET 6.0 SDK, but the upload step failed. I use SFTP to upload the built Zip files to my website and the FTP commands
were failing when negotiating the passive mode connection. I eventually found documentation that Space does not allow
outbound connections from ports 1024-65535. Since the FTP passive mode connection was attempting to open a port in that
range, it was failing.

So I uncovered a hidden requirement. I need to be able to push the built artifacts to my website and I have chosen SFTP.
Space uses outbound bandwidth as a metric for payment.

Now I was in a quandary. The project builds in Space, but I cannot publish the artifacts. The project does not build in
CircleCI, but if it did, I could publish the artifacts. Then I remembered that I have a [Raspberry Pi][raspberry-pi]
that is not being used.

So I started installing [Jenkins][jenkins] on the Raspberry Pi and did not get very far. The install failed towards the
end of the process. As I started searching online I then realized that I do not want to be managing network hardware - I
just want this project to get built and be published.

Then I further realized that the Space configuration had used a Docker container and that CircleCI supported using
Docker containers. So instead of the Windows Orb, I switched to the .NET 6.0 SDK Docker container and the Bash script
and the build started working again.

I have since updated all the `Microsoft.CodeAnalysis` packages again and switched to the .NET 7.0 SDK Docker container.
However, I have come full circle and remained on CircleCI for now. The key point is that by having the build pipeline
mostly in Cake, it was not a big deal to experiment with different service providers.

Also, make sure the build reports failures correctly.

[last-post]: {% post_url 2023-02-08-build-pipeline-decision-process-examples %}
[code-point]: {% post_url 2023-01-04-font-codepoint-source-generator-package %}
[source-generators]: https://devblogs.microsoft.com/dotnet/introducing-c-source-generators/
[appveyor]: https://www.appveyor.com/
[circleci]: https://circleci.com/
[windows-orb]: https://circleci.com/developer/orbs/orb/circleci/windows
[powershell-exit-codes]: https://stackoverflow.com/a/57468523/6951
[jetbrains-space]: https://www.jetbrains.com/space/
[microsoft-docker]: https://hub.docker.com/_/microsoft-dotnet-sdk/
[raspberry-pi]: https://www.raspberrypi.com/
[jenkins]: https://www.jenkins.io/