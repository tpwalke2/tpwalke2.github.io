---
layout: post
title: 'Parsing dates from JSON'
date: '2024-01-29T05:00:00.000-05:00'
author: Tom
tags:
- JSON
- Javascript
- parsing
---
In my most recent [personal project][emotivewords], I store emotion diary entries in the browser's local storage. The
local storage is a key-value store that only allows strings to be stored. So, I convert the diary entries to JSON
strings. One of the properties of the diary entry object is a date. The date is converted to UTC and stored in the
[ISO 8601][iso-8601] format.

When I retrieve the diary entries from local storage, I need to parse the JSON strings into diary entry objects. I use
the JSON.parse() function to parse the JSON strings. However, the date property is parsed as a string. I need to convert
the string back to a date object. I do this by using an [overloaded version][parse] of the JSON.parse() function that
takes a reviver function as a second parameter. The reviver function is called for each property in the JSON string, so
I check to see if the property is the date property and convert it back to a date object.

```javascript
const diaryEntry = JSON.parse(jsonString, (key, value) => {
    if (key === 'entryDate') {
        return new Date(value);
    }
    return value;
});
```

[emotivewords]: {% post_url 2023-12-09-emotivewords-announcement %}
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[parse]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#syntax