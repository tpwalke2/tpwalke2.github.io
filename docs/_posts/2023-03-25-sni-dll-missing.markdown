---
layout: post
title: 'Unable to load SNI.dll'
date: '2023-03-25T05:45:00.000-04:00'
author: Tom
tags:
- .net
- sni.dll
---
I have been working with a team on a project to update a .NET Framework 4.8 ASP.NET web application to .NET 6. Most of
the complicated issues that we have encountered were related to the various dependencies that needed to be updated
through significant breaking changes. However, one error that stumped us for a few days was from the
Microsoft.Data.SqlClient update.

`Unable to load DLL 'Microsoft.Data.SqlClient.SNI.dll' or one of its dependencies: The specified module could not be found. (0x8007007E)`

We scoured the Internet to no avail. We tried numerous solutions on the various StackOverflow answers we found. Still,
whenever the app made a call to the database, that same error message appeared in the logs.

The frustrating thing about the error message is that deep within the `bin` folder, I could find the
`Microsoft.Data.SqlClient.SNI.dll`. Why wasn't the code able to find it?

Eventually, I compared the project file to another project file where I had seen the same dll be published and used
without error. There were two changes we made:
- `RuntimeIdentifier` had been set to `win10-x64`, but we didn't need this specificity, so we removed the `RuntimeIdentifier` property.
- We set the `OutputPath` to explicitly control the output location.

After cleaning and rebuilding the solution with these project changes, we were then able to access the database.