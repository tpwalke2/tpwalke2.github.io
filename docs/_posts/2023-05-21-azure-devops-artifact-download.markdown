---
layout: post
title: 'Azure DevOps Artifact Downloading'
date: '2023-05-21T06:00:00.000-04:00'
author: Tom
tags:
- azure
- devops
- artifacts
---
This past week, I built a pipeline in Azure DevOps that zips the files to be deployed and then stores the zip as a
[build artifact][artifacts] for each of the deployment stages. In each of the deployment stages, I used the
[`DownloadBuildArtifacts@0` task][download-task] to download the artifact so that the pipeline could then unzip and
deploy the files to the appropriate environment.

In the [code review][code-review], a team member helpfully pointed out that Azure DevOps will automatically download
artifacts in some cases. This meant that my build pipeline was duplicating effort in each of the deployment stages - the
artifacts were being downloaded twice. When I looked at the build pipeline steps more closely, sure enough, there was an
initial "Download Artifacts" step before my pipeline steps appeared.

To remove this duplication, I had a couple of options. I could have left the `DownloadBuildArtifacts` task in the
pipeline and turned off the [automatic download][step-download] by specifying `- download: none`. Instead, I chose to
work with the automatic download and I removed the `DownloadBuildArtifacts` task.

The next problem to solve, however, is that I did not know how to access the automatically downloaded artifact. I had
been downloading to the [`Build.ArtifactStagingDirectory`][build-variables] build variable. The automatically downloaded
artifacts were placed in a different directory. The build clearly showed the actual directory where they were
downloaded, I just didn't know how to construct that directory structure using the variables, yet.

I eventually stumbled onto the [`Pipeline.Workspace`][pipeline-variables] predefined variable. This variable contains
the root directory where the pipeline is being run and it corresponds to the root location where the artifacts are
automatically downloaded.

I switched the deployment tasks to point to `Pipeline.Workspace` and was able to remove some duplicated work from the
pipeline.

[artifacts]: https://learn.microsoft.com/en-us/azure/devops/pipelines/artifacts/pipeline-artifacts?view=azure-devops&tabs=yaml
[download-task]: https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/download-build-artifacts-v0?view=azure-pipelines
[code-review]: https://www.atlassian.com/agile/software-development/code-reviews
[step-download]: https://learn.microsoft.com/en-us/azure/devops/pipelines/artifacts/pipeline-artifacts?view=azure-devops&tabs=yaml#download-artifacts
[build-variables]: https://learn.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables-devops-services
[pipeline-variables]: https://learn.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#pipeline-variables-devops-services