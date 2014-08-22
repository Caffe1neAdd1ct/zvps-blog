---
layout: post
title:  "Compiling Apache 2.4.3 for CentOS 6"
date:   2013-02-18 02:35:06
categories: guides linux apache
---

<span><h3>Apache 2.4.3 from Source on a zVPS CentOS 6 Server</h3><strong>If you like running bleeding edge software then you'll like this!</strong></span>
<br><br>
<div class="well well-large">
    <p>
        First lets install the required packages to build Apache httpd:<br>
        <code>yum install gcc gcc-c++ make openssl-devel wget tar</code>
    </p>
    <p>
        Next change directory and get the required source files:<br>
        <code>cd /usr/src/</code><br>
        <code>wget http://mirrors.ukfast.co.uk/sites/ftp.apache.org/httpd/httpd-2.4.3.tar.gz</code><br>
        <code>wget http://apache.mirrors.timporter.net//apr/apr-1.4.6.tar.gz</code><br>
        <code>wget http://apache.mirrors.timporter.net//apr/apr-util-1.4.1.tar.gz</code><br>
    </p>
    <p>
        Now set your source files free .... Well unzip them at least!<br>
        <code>tar xvfz httpd-2.4.3.tar.gz</code><br>
        <code>tar xvfz apr-1.4.6.tar.gz</code><br>
        <code>tar xvfz apr-util-1.4.1.tar.gz</code><br>
    </p>
    <p>
        The APR sources are a requirement for building apache, so move them into the correct folders inside the apache source:<br>
        <code>mv apr-1.4.6 /usr/src/httpd-2.4.3/srclib/apr</code><br>
        <code>mv apr-util-1.4.1/ /usr/src/httpd-2.4.3/srclib/apr-util</code><br>
    </p>
    <p>
        Ok we are almost ready to build Apache, however we will need the PCRE library to compile it, which needs compiling as well, keep going as we are nearly there!<br>
        <code>wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.31.tar.gz</code><br>
        <code>tar xvfz pcre-8.31.tar.gz</code><br>
        <code>cd pcre-8.31</code><br>
        <code>./configure --prefix=/usr/local/pcre</code> <i><< has a period character at the start...</i><br>
        <code>make</code><br>
        <code>make install</code><br>
        <code>cd ..</code><br>
    </p>
    <p>
        Nobody likes a messy server, clean up those archive files we downloaded:<br>
        <code>rm -rf *.tar.gz</code>
    </p>
    <p>
        Finally, lets build Apache:<br>
        <code>cd httpd-2.4.3</code><br>
        <code>./configure --with-pcre=/usr/local/pcre --enable-ssl --enable-so --with-included-apr</code><br>
        <code>make</code><br>
        <code>make install</code><br>
        <code>/usr/local/apache2/bin/apachectl start</code><br>
    </p>
    <p>
        <i>
            With special thanks to Tim at <a href="http://www.prestatips.dk/" title="Tim's Website">www.prestatips.dk</a> for sending an changes and updates to our guide!
        </i>
    </p>
</div>