---
layout: post
title:  "zVPS Blog and Jekyll"
date:   2014-07-28 17:01:24
categories: news
---

Welcome to our blog, a posting ground for a range of useful guides, tips and tricks.

We have opted to run our blog on a piece of software called [Jekyll][jekyll]. 
It's a markdown to static HTML processor, providing an alterative to dynamic blogging software such as WordPress or Drupal. 
Our reasons for using Jekyll include: 

 * Does not require server side languages
 * Not required to be driven by a database server
 * Minimises security risks to our servers
 * Compiles to flat HTML markup

Using a mixture of markdown and liquid templating we have produced this entire blogging site without any server side languages or databases.

![Jekyll Logo](/img/jekyll-logo.png) 

After using Jekyll we can't recommend it enough, install and develop locally, then deploy automatically to live via [Jekyll Hook][jekyll-hook]. Jekyll Hook listens for GitHub Hook requests which fire on push (and many other configurable events) then compiles and deploys your site to live.

[jekyll]:       http://jekyllrb.com
[jekyll-hook]:  https://github.com/developmentseed/jekyll-hook