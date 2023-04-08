---
layout: post
title: 'Multi-select gotcha in AvaloniaUI'
date: '2023-04-08T05:45:00.000-04:00'
author: Tom
tags:
- avaloniaui
- multiselect
---
In [AvaloniaUI][avaloniaui], there are two ways to handle selected items in a `ListBox`, each corresponding to a
different property on the control: `SelectedItems` and `Selection`. The AvaloniaUI team recommends against the use of
`SelectedItems` for large collection, so I started using the `Selection` property instead.

I needed to support multi-selection, so I set the `SelectionMode` property to `Multiple`, created a `SelectionModel` in
my ViewModel, added the `Items`, and started the application. Unfortunately, the ListBox did not allow multiple
selection.

I found a boolean property on the `SelectionModel` named `SingleSelect`. Once I set this property
to `false`, I was able to select multiple items in the ListBox.

[avaloniaui]: https://www.avaloniaui.net/