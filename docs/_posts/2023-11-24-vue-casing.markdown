---
layout: post
title: 'Component Casing in Vue'
date: '2023-11-24T06:00:00.000-05:00'
author: Tom
tags:
- Vue
- Javascript
---
In a [previous post][previous], I mentioned that I was using [Vue][vue] to build a personal website. I have since used
Vue to build another site. Over the next few posts, I will be sharing some of the idiosyncrasies that I have found while
using Vue. In this post, I will be discussing case sensitivity in Vue.

In most Javascript frameworks, the components are named using [PascalCase][pascal-case] while the component properties
are named using [camelCase][camelCase]. Vue is no different.

I have been using the [Single File Component (SFC)][sfc] format for my Vue components. With SFC, the filename of the
component is the name of the component in PascalCase. For example, if I have a component named `MyComponent`, the
filename would be `MyComponent.vue`. The component properties are named using camelCase. For example, the following
shows a set of props for a component:

```javascript
const props = defineProps<{
  pageCount: number;
  startingPage: number;
  align: "start" | "center" | "end";
}>();
```

In React, when you use a component, you use the PascalCase name of the component and the camelCase name of the
properties. For example, if I have a component named `MyComponent` with a property named `myProperty`, I would use the
component like this:

```javascript
<MyComponent myProperty="my value" />
```

In Vue, you can use either the PascalCase name or the [kebab-case][kebab-case] name of the component, but you must use
kebab-case for the properties. For example, the following are all valid ways to use the `MyComponent` component:

```javascript
<MyComponent my-property="my value" />
<my-component my-property="my value" />
```

If you use Vue and a component is not rendering as expected, check the casing of the property names. 

[previous]: {% post_url 2023-06-11-multiple-vue-startup %}
[vue]: https://vuejs.org/
[pascal-case]: https://en.wikipedia.org/wiki/PascalCase
[sfc]: https://vuejs.org/guide/scaling-up/sfc.html
[camelCase]: https://en.wikipedia.org/wiki/Camel_case
[kebab-case]: https://en.wikipedia.org/wiki/Letter_case#Kebab_case