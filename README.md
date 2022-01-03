# CentStream

Setup Minimal Cent OS Stream host. The ssh interface will on a LAN

## Install Virtual Machine

1. Download CentOS Latest
   * [https://www.centos.org/download/]
1. upload to VCenter
1. Create a Vitual Host
1. Install
   * Minimal install
   * Allow root login with Password

## Login

1. Basic Packages
   * dnf install mc net-tools httpd php git zip
1. Install Key, Disable SELinix, lock down ssh, Add sites Config

```bash
mkdir .ssh
chmod 700 .ssh
echo [your key] >.ssh/authorized_keys
chmod 600 .ssh/authorized_keys

# find your ip address
ip -4 addr show | grep global | awk '{ print $2; }'

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
echo IncludeOptional sites-enabled/*.conf>>/etc/httpd/conf/httpd.conf
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

  AccessFileName .htaccess
  <Directory /opt/data/>
    AllowOverride All
    Require all granted
  </Directory>

  <FilesMatch \.php$>
    <If "-f %{SCRIPT_FILENAME}">
      SetHandler "proxy:unix:/run/php-fpm/www.sock|fcgi://localhost"
    </If>
  </FilesMatch>

</VirtualHost>
```

## Create open firewall, Enable Services

```bash
firewall-cmd --zone=public --add-service=http --permanent
systemctl enable httpd php-fpm
```

## create a user

```bash
# add user to users group
useradd -g 100 [username]

# then add keys for auth
cd /home/[username]/
mkdir .ssh
chmod 700 .ssh
echo [your key] >.ssh/authorized_keys
chmod 600 .ssh/authorized_keys
chown -R [username].users .ssh

# make the www writable by users
chgrp -R users /opt/data
chmod -R g+w /opt/data

## reboot

all should work and you can login to remote machine with VSCode ...
