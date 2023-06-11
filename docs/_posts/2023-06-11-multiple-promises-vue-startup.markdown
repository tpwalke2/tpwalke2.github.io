---
layout: post
title: 'Multiple Promises During Vue Startup'
date: '2023-06-11T06:00:00.000-04:00'
author: Tom
tags:
- Vue
- Javascript
- Promise
---
I have been migrating a personal website from a [VanillaJS][vanilla-js] solution to [Vue][vue] to better
[separate the concerns][separation-of-concerns] of different components and also get more experience with Vue.

The website loads data from two different `json` files and then builds a table. The current solution uses an
[XMLHttpRequest][xmlhttprequest] object with callback functions to load the data. This was needed originally because the
[`Promise`][promise] concept was not available across all the browsers I needed to support. I probably could have used a
polyfill, but that would have added more complexity to the solution and more code to download.

`Promises` are now supported across all the browsers that I need to support. So, instead of using callback functions, I
switched to a `Promise`-based solution for loading the `json` files on startup. Now I had [two problems][two-problems]:
still needing to load the data and, now, running asynchronous code during app startup.

I had originally wanted to use `async/await` which would have required the `async` usage of the `created`
[lifecycle][vue-lifecycle] hook. However, my understanding of `async/await` from C# does not match how it works in
Javascript and only one of the calls completed successfully and the other call seemed to never complete.

So, instead of the `async/await` construct, I switched to the former method of using the `.then()` continuation
functions and I moved the loading into the `mounted` lifecycle hook. However, I still had the problem where only one of
the calls would complete.

I found that it is possible to stitch together multiple `Promises` using `Promises.allSettled()`. This method takes an
array of `Promises` and returns a new `Promise` that resolves when all of the original `Promises` have either succeeded
or errored out.

Now I have something like the following:

```javascript
mounted() {
Promise.allSettled(
  [
    fetchJSON('data1.json')
      .then(response => {
        // data1 processing
      }),
    fetchJSON('data2.json')
      .then(response => {
        // data2 processing
      })
  ])
  .then(result => this.isLoaded = true);
}
```

[vanilla-js]: https://stackoverflow.com/a/20435685/6951
[vue]: https://vuejs.org/
[separation-of-concerns]: https://en.wikipedia.org/wiki/Separation_of_concerns
[xmlhttprequest]: https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest
[promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[two-problems]: http://regex.info/blog/2006-09-15/247
[vue-lifecycle]: https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram