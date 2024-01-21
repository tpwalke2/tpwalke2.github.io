---
layout: post
title: '404s with Vue Router URLs in Apache'
date: '2024-01-21T05:00:00.000-05:00'
author: Tom
tags:
- Vue
- Vue Router
- Apache
---
In my most recent [personal project][emotivewords], I used the [Vue][vue] framework and [Vue Router][vue-router] to
handle the routing. I host the site on [Apache][apache] and it uses the [mod_rewrite][mod-rewrite] module to rewrite
URLs. The only rewrite rules I initially had were to redirect all requests to HTTPS and to prevent hot-linking of any
images.

Within the application, links to different pages were correctly handled by Vue Router. However, if I were to use the
links directly in the address bar, I would get a 404 error. This is because Apache, and not Vue Router, was attempting
to handle the URL.

To fix this, I had to update my rewrite rules to redirect certain requests to the index page so that Vue Router could
handle the URLs. I added the following rules to my `.htaccess` file:

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On   # enable the rewrite engine
    RewriteBase /      # set the base URL for relative paths
    
    # Other rules go in here ...
    
    RewriteRule ^index\.html$ - [L]      # don't rewrite index.html
    RewriteCond %{REQUEST_FILENAME} !-f  # if file doesn't exist
    RewriteCond %{REQUEST_FILENAME} !-d  # or if directory doesn't exist
    RewriteRule . index.html [L]         # serve index.html
</IfModule>
```

This was a simple fix and now the URLs to separate pages in the application do not return 404 errors when entered
directly in the address bar.

[emotivewords]: {% post_url 2023-12-09-emotivewords-announcement %}
[vue]: https://vuejs.org/
[vue-router]: https://router.vuejs.org/
[apache]: https://httpd.apache.org/
[mod-rewrite]: https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html