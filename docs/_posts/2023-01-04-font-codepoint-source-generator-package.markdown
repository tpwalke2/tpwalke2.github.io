---
layout: post
title: Generating a strongly-typed enum for font codepoints
date: '2023-01-04T06:00:00.000-05:00'
author: Tom
tags:
- source-generator
- nuget
- enum
- C#
- fonts
- github
---
Most fonts simply have glyphs that correspond to characters in a particular alphabet and are organized
into a known order so that an `a` in Roboto is in the same location as `a` in another font. However,
not all fonts are character fonts. It is possible to use fonts as a form of an icon library.

Icon fonts still contain glyphs in a particular order. However, the glyphs do not necessarily correspond 
with a known alphabet. So typing an `a` in a typical font will display some other character when
switching to one of these icon fonts.

I recently ran into a need to incorporate an [icon font][material-icon] into a project. In addition to
the ttf file, this font comes with a `.codepoints` file. The `.codepoints` file is simply a text file
with lines that map to each glyph in the font. Each line contains a name for the glyph and the 2-byte
hex value that makes that glyph appear.

```
abc eb94
ac_unit eb3b
access_alarm e190
access_alarms e191
access_time e192
access_time_filled efd6
accessibility e84e
accessibility_new e92c
```

I want to make it as easy as possible to refer to particular glyphs in the font when using it in the
user interface I am building. Rather than storing a dictionary in memory and using a set of known
constants to address these values, I prefer to exploit the strongly-typed system in C#. This type
of map between a word and a numeric value seems like a good fit for an enum.

The problem now is that I have to manually build an enum for this font and any other font that I may
encounter in the future. Why do something manually, when I can [automate][automate-task] it instead?

I built a [source code generator][generator] that reads `.codepoints` files in a C# project and then
generates an enum. The project is open-sourced with the [MIT license][license] and is available on
[GitHub][generator]. It is also available as a [Nuget package][nuget].

With this project I was able to explore the source code generation feature in C#. I also set it up with
a continuous integration and continuous deployment pipeline in [AppVeyor][pipeline]. Whenever a commit
is pushed to the `main` branch, the pipeline builds, tests, packages, creates a GitHub release, and
uploads to NuGet.

[material-icon]: https://fonts.google.com/icons?icon.set=Material+Icons
[automate-task]: https://xkcd.com/1205/
[generator]: https://github.com/tpwalke2/CodePointEnumGenerator
[license]: https://github.com/tpwalke2/CodePointEnumGenerator/blob/main/LICENSE
[nuget]: https://www.nuget.org/packages/CodePointEnumGenerator
[pipeline]: https://ci.appveyor.com/project/tpwalke2/codepointenumgenerator