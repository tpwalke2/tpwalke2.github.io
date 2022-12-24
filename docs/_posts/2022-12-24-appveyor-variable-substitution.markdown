---
layout: post
title: AppVeyor Variable Subsitution
date: '2022-12-24T08:00:00.000-05:00'
author: Tom
tags:
- appveyor
- ci/cd
---
A common need in CI/CD pipelines is to pass variables (e.g. the current build number) from one system to another. I
recently encountered this need with an AppVeyor configuration that executes a Powershell script.

Despite the [existing documentation][appveyor-docs], the variable substitution for `{build}` was simply passing the
string value of `{build}` into the Powershell script.  Thanks to a [forum user][appveyor-forum], I found out that
variable substitution has different syntax depending on the context. Instead of `{}`, you have to surround the variable
with `%%`.

Changing from `powershell .\bootstrap.ps1 {build}` to `powershell .\bootstrap.ps1 %build%` made the build number flow to
the Powershell script as I originally intended.

[appveyor-docs]: https://www.appveyor.com/docs/build-configuration/#build-versioning
[appveyor-forum]: https://help.appveyor.com/discussions/questions/31318-using-variables-in-appveyoryml