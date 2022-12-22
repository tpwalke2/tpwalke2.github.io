---
layout: post
title: Blog Migration
date: '2022-12-22T08:00:00.000-05:00'
author: Tom
tags:
---
Surprise, I moved!

Not my physical location, but the blog location.

I moved from Blogger to GitHub Pages and am using [Jekyll][jekyll] as a static site generator. There are no more ads but also no more comments. You can find my email address in the footer if you would like to get in touch.

In the switch, I ran into a problem where the Blogger importer kept returning an error message. The command `jekyll import blogger...` did not recognize the parameters I tried to pass in. The error message seemed to indicate that the `jekyll-import` package had not been installed.

But I distinctly remembered running `gem install jekyll-import`. The command-line history backed up my assertion.

It turns out that you have to reference the `jekyll-import` package in the Gemfile for the site.

{% highlight ruby%}
#gem "jekyll", "~> 4.3.1"
gem "jekyll-import", "~> 0.21.0"
# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
{% endhighlight %}

You can see the original Gemfile in the [repository]. It no longer includes the reference to `jekyll-import`, because the Blogger import only needed to occur once.

Feel free to look around the repository. I have made the content available with the [CC BY 4.0 License][cc-license].

[jekyll]: https://jekyllrb.com/
[repository]: https://github.com/tpwalke2/tpwalke2.github.io
[cc-license]: https://creativecommons.org/licenses/by/4.0/