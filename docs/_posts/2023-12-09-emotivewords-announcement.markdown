---
layout: post
title: 'New Project: Emotive Words'
date: '2023-12-09T05:00:00.000-05:00'
author: Tom
tags:
- Vue
- Javascript
- emotions
---
At the last company that I worked for, my team used [Scrum][scrum] to manage our work. In Scrum, there are a few
meetings that are held on a regular basis. One of these meetings is the retrospective. In the retrospective, the team
discusses what went well and what could be improved.

I was the facilitator for these retrospectives and I would have everyone answer the two questions "What went well?" and
"What could be improved?". We would then discuss ideas for how we could address the things that could be improved. While
the consistency of the questions was helpful to see improvement over time, I felt that we got into a rut with the process.

I am now working for another company where we still use Scrum. I am not the facilitator for the retrospectives. Our
facilitator does a great job of keeping the retrospectives fresh. One of the things that she does is to find different
sets of questions to ask.

One week, she asked us to think of one word to describe how we were feeling. She had the foresight to understand that
some of us would have a hard time coming up with a word and shared this [emotional wheel diagram][diagram] to help us. I
found the diagram to be very helpful due to the fact that I have a hard time coming up with words to describe how I am
feeling. I have since used the diagram to help me describe how I am feeling.

While the wheel diagram is helpful, I also find it to be overwhelming with the number of words that are visible at the
same time. I thought that it could be helpful to have a tool that would allow you to step through each layer of the
diagram and only show the words for that layer. I built that tool and named it [Emotive Words][emotive-words].

Emotive Words was written in [Vue][vue]. It is a single-page application that uses Vue Router to manage the routes. The
application is hosted on a Linux server running [Apache][apache]. The application offers a diary feature that allows
you to record how you are feeling each moment. The diary entries are stored in the browser's local storage and can be
exported to a JSON file.

[scrum]: https://www.scrum.org/resources/what-scrum-module
[diagram]: https://imgur.com/a/CkxQC
[emotive-words]: https://www.emotivewords.com/
[vue]: https://vuejs.org/
[apache]: https://httpd.apache.org/