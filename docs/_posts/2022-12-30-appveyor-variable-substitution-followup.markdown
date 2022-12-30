---
layout: post
title: AppVeyor Variable Subsitution Follow-Up
date: '2022-12-30T06:00:00.000-05:00'
author: Tom
tags:
- appveyor
- continuous-integration
---
In the [previous post][previous], I noted that the variable substitution
requires a slightly different syntax depending on the context. Well, I ran into an additional hurdle when trying to pass
environment variables to the aforementioned Powershell script.

I had been using `%build%` to pass the current build number. I also needed to pass the current branch and working
directory, both of which are stored in the `APPVEYOR_REPO_BRANCH` and `APPVEYOR_BUILD_FOLDER` respectively. So I applied
the lesson from before and tried to use `powershell .\bootstrap.ps1 -buildNumber %build% -branch "%APPVEYOR_REPO_BRANCH%" -buildPath "%APPVEYOR_BUILD_FOLDER%"`
which did not work. The build number did not get replaced as expected.

I changed the script invocation to `powershell .\bootstrap.ps1 -buildNumber %APPVEYOR_BUILD_NUMBER% -branch "%APPVEYOR_REPO_BRANCH%" -buildPath "%APPVEYOR_BUILD_FOLDER%"`
instead and the build number went into the Powershell script as expected.

I concluded that the special variables (like `{build}`) do not mix with environment variable substitutions.

[previous]: {% post_url 2011-02-25-intent %}