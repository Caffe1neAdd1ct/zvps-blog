---
layout: post
title:  "Compiling Apache 2.4.3 for CentOS 6"
date:   2014-07-18 02:35:06
categories: guides linux apache
---


If you like running up-to-date packages and getting at the new features they provide, then compiling apache from source is a good start. 
Here is our basic guide to get you up and running with the latest version:

1) Install required packages through the CentOS package manager:
{% highlight sh %}
    yum install gcc gcc-c++ make openssl-devel wget tar
{% endhighlight %}
2) Download Apache source files and APR (required dep):
{% highlight sh %}
cd /usr/src/`
wget http://mirrors.ukfast.co.uk/sites/ftp.apache.org/httpd/httpd-2.4.3.tar.gz
wget http://apache.mirrors.timporter.net//apr/apr-1.4.6.tar.gz
wget http://apache.mirrors.timporter.net//apr/apr-util-1.4.1.tar.gz
{% endhighlight %}
3) Unzip the source files:
{% highlight sh %}
tar xvfz httpd-2.4.3.tar.gz
tar xvfz apr-1.4.6.tar.gz
tar xvfz apr-util-1.4.1.tar.gz
{% endhighlight %}
4) The APR sources are a requirement for building apache, so move them into the correct folders inside the apache source:
{% highlight sh %}
mv apr-1.4.6 /usr/src/httpd-2.4.3/srclib/apr
mv apr-util-1.4.1/ /usr/src/httpd-2.4.3/srclib/apr-util
{% endhighlight %}
5) Almost ready to build Apache, however we will need the PCRE library to compile it, which in turn needs compiling:
{% highlight sh %}
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.31.tar.gz
tar xvfz pcre-8.31.tar.gz
cd pcre-8.31
./configure --prefix=/usr/local/pcre
make
make install
cd ..
{% endhighlight %}
6) Finally, lets build Apache:
{% highlight sh %}
cd httpd-2.4.3
./configure --with-pcre=/usr/local/pcre --enable-ssl --enable-so --with-included-apr
make
make install
/usr/local/apache2/bin/apachectl start
{% endhighlight %}
