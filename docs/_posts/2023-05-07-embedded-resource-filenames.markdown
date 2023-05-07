---
layout: post
title: 'Embedded Resource Not Found in Project'
date: '2023-05-07T06:00:00.000-04:00'
author: Tom
tags:
- msbuild
- embedded resource
- jetbrains
- rider
- culture
---
I wrote a utility recently where I decided to use embedded resources to store templates that would later be populated
for the program output. One of the templates was meant to populate a concept of "metadata" for the output. So I used a
filename in the format `<prefix>.metadata.txt`. There were two other template files and all three files were set to a
build action of `EmbeddedResource`.

When I ran the utility, it appeared as if the metadata template had not been loaded. The metadata was being generated as
the empty string, which was the fallback in case the metadata file was not used or able to be loaded.

I ran the utility with debugging on to see if I had missed some logic in the metadata generation. Only two of the
template files were available. The metadata template had not been loaded.

I verified the build action settings - all three template files matched. I cleaned and rebuilt the solution. I even
deleted the `bin` and `obj` folders, restarted the IDE and rebuilt the solution. This is my standard troubleshooting, by
the way, to ensure that there are no rogue leftover build artifacts complicating matters.

In each case, the metadata template still did not load and the utility did not generate the expected output.

I had worked through all the troubleshooting steps I found in various posts around the Internet. This was a case where
it seemed as if I was encountering a new, to me, problem. Clearly the metadata file was configured in the project file
correctly, yet it was not getting embedded into the final assembly. I needed to get more information about the build
process.

I primarily use JetBrains Rider for .NET development now, and thankfully I stumbled across the context-menu option to
`Build selected project with diagnostics`. I ran the diagnostics build option and then a new tab was opened with the
full output from the build.

When I searched for the name of the metadata template file, one of the references indicated that a culture of `metadata`
had been determined for it. Unfortunately, I forgot to note the exact text of the entry, so I cannot share that here.
However, that entry made me realize that the build process thought that the metadata template file was a different
[culture][culture] and that the determination was based on the suffix `.metadata.txt`.

Since I was not attempting to define a new `metadata` culture, I changed the filename to `<prefix>-metadata.txt`. After
I cleaned and rebuilt the solution, when I ran the utility with debugging on, all three template files were available
and the utility generated the correct metadata.

In preparing this post, I found that I am not the only one to have encountered this issue. There was an
[update][attribute] to MSBuild 16.9 to override the default behavior. So, if your requirements mean that you cannot
rename the file as I did, and you are using MSBuild 16.9+, you can update the `EmbeddedResource` and add the
`WithCulture="false"` attribute.

[culture]: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-7.0
[attribute]: https://github.com/dotnet/msbuild/pull/5824