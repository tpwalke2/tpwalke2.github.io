---
layout: post
title: 'Troubleshooting Minecraft Mod Updates'
date: '2025-12-18T07:00:00.000-05:00'
author: Tom
tags:
  - Minecraft
  - Fabric
  - Gradle
---

Mojang released a new version of Minecraft which means that I need to update my [mod](https://modrinth.com/mod/bluemap-sign-markers)
to 1.21.11.

I remapped the source using the latest values for the different dependencies from the [Fabric dev portal](https://fabricmc.net/develop/)
and then updated my `gradle.properties` file. Then, when I attempted to build the mod, I received a stack trace with the
main error message of `No matching variant of net.fabricmc:fabric-loom:1.14.7`.

Since the error message did not provide immediate clues for resolution, I searched in the Fabric Discord history and
found a simple solution: I needed to update gradle.

I stashed my changes, then ran the command to update gradle (`./gradlew wrapper --gradle-version latest`), and unstashed
the changes. Now it builds and runs on 1.21.11.