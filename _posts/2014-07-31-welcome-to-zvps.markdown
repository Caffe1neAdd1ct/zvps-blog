---
layout: post
title:  "Welcome to the zVPS company blog."
date:   2014-07-28 17:01:24
categories: news
---

Welcome to our zVPS staff blog, a posting ground for a range of useful guides, tips and tricks.

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
