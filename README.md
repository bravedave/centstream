# Centstream

Setup Minimal Cent OS Stream host. The ssh interface will on a LAN

1. Download CenOS Latest
  a. upload to VCenter
  b. Create a Vitual Host
  c. Install
    * Minimal install
    * Allow root login with Password
1. Login
  a. Basic Packages
    i. yum install mc httpd php
  a. Install Key
    * mkdir .ssh
    * chmod 700 .ssh
    * echo [your key] >.ssh/authorized_keys
    * chmod 600 .ssh/authorized_keys
  b. Set root login to
1. Add sites Config
  a. mkdir /etc/httpd/sites-enabled/
  b. And add a line to the Apache configuration file:
    * ```Include sites-enabled/*.conf```
1. Add a Site
  a. mkdir -p /opt/data/sites/www/public
  b. echo "hello world" >>/opt/data/sites/www/public/index.html
  c. create config

```config
<VirtualHost 111.111.111.111:80 >
  ServerName [yourserver]
  DocumentRoot /opt/data/sites/www/public/
  CustomLog /opt/data/logs/yourserver_access_log combined
  ErrorLog /opt/data/logs/yourserver_error_log
  AddType application/x-httpd-php .php
</VirtualHost>
```
