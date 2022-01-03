# Centstream

Setup Minimal Cent OS Stream host. The ssh interface will on a LAN

## Download CenOS Latest

1. upload to VCenter
1. Create a Vitual Host
1. Install
   * Minimal install
   * Allow root login with Password

## Login

1. Basic Packages
   * dnf install mc httpd php
1. Install Key, Disable SELinix, lock down ssh, Add sites Config

```bash
mkdir .ssh
chmod 700 .ssh
echo [your key] >.ssh/authorized_keys
chmod 600 .ssh/authorized_keys

# bind ssh to lan port
sed -i 's/^[#]ListenAddress 0.*/ListenAddress 111.111.111.111 /g' /etc/ssh/sshd_config

# Set root login to prohibit-password
sed -i 's/^[#]PermitRootLogin.*/PermitRootLogin prohibit-password/g' /etc/ssh/sshd_config
sed -i 's/^[#]PasswordAuthentication.*/PasswordAuthentication no/g' /etc/ssh/sshd_config

# Disable SELinux
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config

# Add sites Config
mkdir /etc/httpd/sites-enabled/
# add a line to the Apache configuration file:
echo Include sites-enabled/*.conf>>/etc/httpd/conf/httpd.conf
```

## Add a Site

```bash
mkdir -p /opt/data/sites/www/public
echo "hello world" >>/opt/data/sites/www/public/index.html
```

## create config

```config
<VirtualHost 111.111.111.111:80 >
  ServerName [yourserver]
  DocumentRoot /opt/data/sites/www/public/
  CustomLog /opt/data/logs/yourserver_access_log combined
  ErrorLog /opt/data/logs/yourserver_error_log
  AddType application/x-httpd-php .php
</VirtualHost>
```

## reboot
