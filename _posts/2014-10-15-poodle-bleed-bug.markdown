---
layout: post
title:  "Poodle Bleed Bug"
date:   2014-10-15 13:30:00
categories: security
---

### Summary ###

The newly announced Poodle Bleed Bug allows attackers to intercept traffic sent over SSL (https) connections and decrypt any information sent using the SSLv3 protocol.

Most of the internets servers are setup by detail to fallback to this protocol if a client (such as a browser or email client) requests to use it.

### Impact ###

This will impact people browsing sites which only support up to SSLv3 and people using out-of-date browser which also support up-to SSLv3. We would recommend disabling this on all services implementing SSL for secure connections ASAP, common services on a Linux server would include:

 * Apache 2
 * Postfix
 * Dovecot
 * OpenVPN
 * Nginx
 * NodeJS

