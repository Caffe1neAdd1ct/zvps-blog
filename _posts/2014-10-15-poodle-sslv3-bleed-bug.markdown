---
layout: post
title:  "Poodle SSLv3 Bleed Bug"
date:   2014-10-15 13:30:00
categories: security
---

### Summary ###

The newly announced Poodle Bleed Bug allows attackers to intercept traffic sent over SSL (https) connections and decrypt any information sent using the SSLv3 protocol.

Most of the internets servers are setup by detail to fallback to this protocol if a client (such as a browser or email client) requests to use it.

<hr>

### Impact ###

This will impact people browsing sites which only support up to SSLv3 and people using out-of-date browser which also support up-to SSLv3. We would recommend disabling this on all services implementing SSL for secure connections ASAP, common services on a Linux server would include:

 * Apache 2
 * Postfix
 * Dovecot
 * OpenVPN
 * Nginx
 * NodeJS

<hr>

### Disabling SSLv3 on your Browser(s) ###

#### Internet Explorer ####

![Disable SSLv3 IE](/img/ie-ssl3-disable.png) 

#### Chrome ####

 - Windows
 Be aware this will only protect you if chrome is opened from the modified shortcut!

![Disable SSLv3 Chrome](/img/chrome-ssl3-disable.png) 

 - Linux

vi /usr/share/applications/google-chrome.desktop

Search for Exec

Add --ssl-version-min=tls1 to the end of all Exec lines

#### Firefox ####

Mozilla plan to disable SSLv3 support in version 35, however this release is planned for the 25th November 2014. It is recommended to install the following plugin and use it to disble SSLv3 communication in the meanwhile:

https://addons.mozilla.org/en-US/firefox/addon/ssl-version-control/

<hr>

### Linux System Administrators - Testing Services ###

    openssl s_client -connect secure.assetsource.com:443 -ssl3 | grep -i "sslv3 alert handshake failure"

If the string "sslv3 alert handshake failure" **is NOT found then the site is at risk** from eavesdroppers and attackers could login as people using the site.

If the string **is found then the server is fine**, example of a secure site:

    openssl s_client -connect portal.zvps.uk:443 -ssl3 | grep -i "sslv3 alert handshake failure"

Our servers have always run with SSLv3 disabled.

<hr>

### Linux System Administrators - Disabling SSLv3 on Services ###

#### Apache #### 

Sample SSL virtualhost with only TLS enabled:

    ## Redirect Virtual Host ##
    <VirtualHost *:80>

        ServerName domain
        ServerAlias domain
        ServerAdmin support@domain

        DocumentRoot /var/www/html/domain

        RewriteEngine On
        RewriteCond %{HTTPS} off
        RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]

    </VirtualHost>

    ## SSL Virtual Host ##
    <VirtualHost *:443>

        ServerName domain
        ServerAlias domain
        ServerAdmin support@domain

        DocumentRoot /var/www/html/domain/

        <Directory "/var/www/html/domain/">
            php_value error_reporting 22519
            Order allow,deny
            Allow from all
        </Directory>

        SSLEngine on
        SSLCertificateFile    /etc/ssl/certs/domain.crt
        SSLCertificateKeyFile /etc/ssl/certs/domain.key
        SSLCACertificateFile  /etc/ssl/certs/global-and-intermediate.crt

        SSLProtocol -ALL +TLSv1
        SSLHonorCipherOrder on
        SSLCipherSuite HIGH:!aNULL:!MD5

        ErrorLog /var/log/httpd/domain.error.log
        CustomLog /var/log/httpd/domain.access.log combined

    </VirtualHost>

Apache 2.4 can also be improved to specify versions of TLS, **only add the versions you would like your server to provide**:

    SSLProtocol -ALL +TLSv1.0 +TLSv1.1 +TLSv1.2

Basically after any declaration of `SSLEngine on` add:

    SSLProtocol -ALL +TLSv1
    SSLHonorCipherOrder on
    SSLCipherSuite HIGH:!aNULL:!MD5


#### Nginx ####

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

#### Postfix ####

    smtpd_tls_mandatory_protocols=!SSLv2,!SSLv3

####Dovecot ####

    ssl_cipher_list = HIGH:MEDIUM:+TLSv1:!SSLv2:!SSLv3

#### OpenVPN ####

    tls-cipher TLS-ECDHE-RSA-WITH-AES-256-GCM-SHA384

or

    tls-cipher TLS-ECDHE-RSA-WITH-AES-256-GCM-SHA384:TLS-ECDHE-ECDSA-WITH-AES-256-CBC-SHA

Be sure any of the ciphersuites is unsupported in SSLv3.


