---
layout: post
title:  "Welcome to zVPS!"
date:   2014-07-28 17:01:24
categories: news
---

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

{% highlight apache %}
<VirtualHost *:80>

    ServerName dev.zvps.uk
    ServerAlias dev.zvps.uk www.dev.zvps.uk
    ServerAdmin support@zvps.co.uk

    DocumentRoot "/var/www/html/dev.zvps.uk/"

    <Directory "/var/www/html/dev.zvps.uk">
        AllowOverride All
        Order deny,allow
        Deny from all
        Allow from 213.104.155.4
        Allow from 151.228.214.45
        Allow from 81.2.103.70
    </Directory>

    ErrorLog  "/var/log/httpd/dev.zvps.uk-error.log"
    CustomLog "/var/log/httpd/dev.zvps.uk-access.log" combined
    CustomLog "/var/log/httpd/dev.zvps.uk-bandwidth.log" common
</virtualhost>
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll]:    http://jekyllrb.com
