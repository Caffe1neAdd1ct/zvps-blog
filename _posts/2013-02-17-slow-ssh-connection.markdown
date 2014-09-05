---
layout: post
title:  "Slow SSH password connection prompt"
date:   2013-02-17 02:35:06
categories: guides linux ssh
---


![SSH Terminal Logo](/img/ssh.png) 


Sometimes you may notice your VPS or Dedciated server is extremely slow responding to an SSH connection attempt. 
There are two main settings you can tweak to help speed up the connection process:

Find the following file on the server and open it using <code>vi</code>:
{% highlight sh %}
    vi /etc/ssh/sshd_config
{% endhighlight %}
Next find the following directives and change them to "no", make sure they are uncommented (don't have a # at the start of the line):
{% highlight sh %} 
UseDNS no
GSSAPIAuthentication no
{% endhighlight %}

Save the file by typing <code>:wq</code> followed by the return or enter key. 

Finally to enable these changes you need to restart SSH daemon by running one of the following commands:

For CentOS or Fedora:
{% highlight sh %} 
service sshd restart
{% endhighlight %}

For Debian or Ubuntu:
{% highlight sh %} 
service ssh restart
{% endhighlight %}

Press <code>ctrl + d</code> to disconnect, then reconnect and the password prompt should be almost instant. 

If you are using a password to authenticate when using ssh we would highly recommend researching and implementing key pair (pre-shared) authentication.