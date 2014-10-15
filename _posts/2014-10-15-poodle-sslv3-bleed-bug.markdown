---
layout: post
title:  "Poodle SSLv3 Bleed Bug"
date:   2014-10-15 13:30:00
categories: security
---

### Summary ###

The newly announced POODLE (Padding Oracle On Downgraded Legacy Encryption) bug allows attackers to intercept traffic sent over the SSLv3 protocol and decrypt it.

Most servers are setup to fallback to SSLv3 by default if a browser or email client requests to use an older protocol.

<hr>

### Impact ###

This will impact anyone browsing sites or using services which only support up to SSLv3. We would recommend disabling SSLv3 on all services implementing SSL for a secure connection, common services on a Linux server could include:

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

 - Crome on a Windows PC

 Beware - this will only protect you if Chrome is opened from the modified shortcut!

Right click your Chrome shortcut icon
Select Properties
Add `--ssl-version-min=tls1` to the end of the Target text box
(outside the quotes) as seen in the image below,
Click Apply then click Ok.

![Disable SSLv3 Chrome](/img/chrome-ssl3-disable.png) 

 - Crome on a Linux PC

Open a new terminal and run the following command:
vi /usr/share/applications/google-chrome.desktop

Search for all instances of `Exec` and add `--ssl-version-min=tls1` to the end of all Exec lines

#### Firefox ####

Mozilla already plan to disable SSLv3 support in version 35, which is scheduled for release on the 25th November 2014. To maximise security in the meantime it is recommended to install the below plugin to disable SSLv3 communication until the next Mozilla update:

https://addons.mozilla.org/en-US/firefox/addon/ssl-version-control/

<hr>

### Linux System Administrators - Testing Services ###

    openssl s_client -connect secure.assetsource.com:443 -ssl3 | grep -i "sslv3 alert handshake failure"

If the string "sslv3 alert handshake failure" **is NOT found then the site/ service IS at risk** and data may be intercepted by attackers.

If the string **IS found then the server IS secure**, example of a secure site:

    openssl s_client -connect portal.zvps.uk:443 -ssl3 | grep -i "sslv3 alert handshake failure"

Check out our easy to use Linux test script to see if your server is secure [our poodle test script][poodle-test-script] .

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

Apache 2.4 can also be improved to specify versions of TLS, **only add the versions you want your server to provide**:

    SSLProtocol -ALL +TLSv1.0 +TLSv1.1 +TLSv1.2

After any declaration of `SSLEngine on` add:

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

Be sure none of the ciphersuites are supported in SSLv3.


[poodle-test-script]:  https://gist.github.com/Caffe1neAdd1ct/75040d8b2daa5bf243f2