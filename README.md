# **LAMPS Creation by Hand**

## Summary

### **Before everything**
- **SSH & IPFIXE**
- **Basic tools**
  
### **Installation & Discorvery of Apache2**
- **Installing Apache2**
- **Creating vHost for WordPress**
- **Securing Apache2**

### **Installation & Discovery of MariaDB**
- **Installing MariaDB**
- **Securing MariaDB**
- **Creating user for WordPress**

### **Installing PHP**
- **Checking PHP version**
- **Checking script modules PHP**

### **WordPress**
- **Installing WordPress**
- **Setting up WordPress**

### **WAN**
- **How to get my public IP**
- **Set a A record with my public IP**
- **Modification of the vHost & port.conf**
- **Add the domaine name & port on WordPress**

### **HTTPS**
- **Cerbot & Let's Encript certificate**
---

## **Before everything**

### **SSH & IPFIXED**

Before everything let's fixed the IP address of the machine :

```where do I find the network file
nano /etc/network/interfaces
```
Then when I am in this file I put those parameters in it : 

(*Make sure to adapt the parameters for the situation*)

```Parameters for a fixed IP
iface ens192 inet static
address my IP address
gateway my Gateway
```
When it's done restart the networking :

```
systemctl restart networking
```
Now with my IP fixed let's get to SSH :

```Installing SSH
apt -y install ssh
````

I will install SUDO for usefull purpuses :

```Installing SUDO
apt -y install sudo
```
Then I will put my user in the sudoers file :

```Add a user to sudoers
usermod -aG sudo youruser
```
And I will do this command to check if is in the sudoers group :

```Checking groups of a user
groups
```
**And before I configure the sshd_config file I will add a ssh key :**

At the root, I will create a file for my key :

```Creating a file
mkdir .ssh
```
Then I put my ssh key in it :

```Path to the .ssh/authorized_keys
sudo nano .ssh/authorized_keys
```

Now I will secure my SSH in the sshd_config file :

```Sshd_config file path
nano /etc/ssh/sshd_config
```

#### When I am in the sshd_config file I will do this list of steps :
   ! Before any modification open another ssh window before and create a bakup file !
```Backup file for sshd_config
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```


   (*change the (*****)with the actual file*)


  - **Decomment PermitRootLogin Yes & change it to No**
  - **Change the LoginGraceTime 120 to 30**
  - **Add a MaxAuthTries set with only 3 connexions tries**
  - **Add a MaxSessions parameter with only 3 sessions autorized**
  - **Add the AlllowUsers with all the users you need to have**
  - **Declared the autorized algorithms with HostKeyAlgorithms**
  - **Forbid the interactive keyboard with PasswordAuthentication set on No**
  - **Pass the StrictModes parameter to Yes**
  - **Declared explicitly the emplacement of the SSH keys in AuthorizedKeyFile**
  
```Secure SSH
  LoginGraceTime 30
  AllowUsers watermeloncybercat
  PasswordAuthentication no
  PermitRootLogin no
  StrictModes yes
  MaxAuthTries 3
  MaxSessions 3
  HostKeyAlgorithms ssh-rsa,ssh-ed25519
  PubkeyAuthentication yes
  AuthorizedKeyFile     ***************
```

#### Now that my IP is fixed and my ssh is secured let's get some basic tool.

### **Basic tools**

There is some tools that are useful for a LAMPS :

```Basic tools
sudo apt -y install \
  ca-certificates \
  ccze \
  curl \
  htop \
  iptables-persistent \
  man \
  neofetch \
  net-tools \
  tcpdump \
  unzip \
  vim \
  wget \
  zip
```

#### **Alright everything is set let's get to the main course !**

## **Installation & Discorvery of Apache2**

### **Installing Apache2**

```Installing Apache2
sudo apt -y install apache2
```

**When it's done I go check with the Public IP that the apache2 page is up.**

### **Creating vHost for WordPress**

```
sudo nano /etc/apache2/sites-available/website.conf
```
Then I will enable some modules for apache2 :

```Modules for Apache2
sudo a2enmod rewrite
sudo a2enmod deflate
sudo a2enmod headers
sudo a2enmod ssl
```
Between each module restart apache2 :

```Restart Apache2
sudo systemctl restart apache2
```
And I will get the apache2-utils packtets :

```apache2-utils packets install
sudo apt-get install -y apache2-utils
```

**Exemple of a vHost**

```
<VirtualHost *:80>
    ServerName your.site.net
    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # Logs
    ErrorLog ${APACHE_LOG_DIR}/wordpress-error.log
    CustomLog ${APACHE_LOG_DIR}/wordpress-access.log combined

</VirtualHost>
```
### **Securing Apache2**

#### **Now I will secure apache2**

But before anything let's create a backup file :

```Create a backup file 
sudo cp /etc/apache2/conf-enable/security.conf /etc/apache2/conf-enable/security.conf.bak
```

In the security.conf file let's do some changes :

  - **Decomment SeverTokens and set them to Prod**
  - **Add the ServerSignature and set it to Off**
  - **Add TraceEnable and set it also on Off**
  - **Add some mod Headers**

```Secure Apache2
ServerTokens Prod
ServerSignature Off
TraceEnable Off
<IfModule mod_headers.c>

        Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        Header set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://ajax.googleapis.com/ https://www.google.fr/        
        Header always set X-Frame-Options "DENY"
        Header always set X-Content-Type-Options "nosniff"
        Header always set Referrer-Policy "no-referrer"
        Header always set Permisssions-Policy "geolocalisation=(), microphone=(), camera=()"

</Ifmodule>
```

## **Installation & Discovery of MariaDB**

### **Installing MariaDB**

```Installing MariaDB
sudo apt -y install mariadb-server
```

### **Securing MariaDB**

```Secure MySQL
sudo mysql_secure_installation
```
When the secure installation of mySQL start it will ask some question answer it like this :

```Questions of mySQL installation
Switch to unix_socket authentication [Y/n] Y
Change the root password? [Y/n] n
Remove anonymous users? [Y/n] y
Disallow root login remotely? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y
```


### **Creating user for WordPress**

I will start by connecting myself to mySQL :

```Connection to mySQL
sudo mysql
```

Then I will do the following actions :

 - **Create a Database**
 - **Create a user with a password**
 - **Grant all privileges of the database that I created to the user**
 - **Then I flush the privileges**
  
```MySQL preparation
CREATE DATABASE wordpress;
CREATE USER 'catwithamelon'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON wordpress.* TO 'catwithamelon'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

To be sure I can do this commands to remind me what I have done :

```Reminder
SHOW Databases;
SELECT User, Host, Password FROM mysql.user;
EXIT;
```

## **Installing PHP**

I will now install PHP and all its dependences :

```Installing PHP & the dependences
sudo apt install -y \
  php \
  php-mysql \
  php-curl \
  php-gd \
  php-mbstring \
  php-xml \
  php-xmlrpc \
  php-zip \
  php-intl \
  php-bcmath
  ```

  ### **Checking PHP version**

  How can I check my PHP version :

  ```Checking PHP version
  php -v
  ```

### **Checking script modules PHP**

Now to verified that the PHP modules are active I will create a file name "phpinfo.php" at the root of my website :

```Creating phpinfo.php
sudo nano /var/www/html/phpinfo.php
```
Then in the file I put the following code :

```phpinfo.php code
<?php
phpinfo();
?>
```
I can verified the website with this :

**http://websiteIP/phpinfo.php**

## **WordPress**

### **Installing WordPress**

Now I will install WordPress :

```Installing WordPress
sudo curl -O https://wordpress.org/latest.tar.gz && \
sudo tar -xzf latest.tar.gz -C /var/www/html/ && \
rm latest.tar.gz
```
```
sudo chown -R www-data:www-data /var/www/html/wordpress && \
sudo chmod -R 755 /var/www/html/wordpress
```

### **Setting up WordPress**

I will connect to the local MariaDB Database I have previously created also using the previously defined credentials :

* **Username**
  * catwithamelon
* **Password**
  * mypassword

Then I will create a User for WordPress :

* **Username**
  * cybercat
* **Password**
  * mypassword


## **WAN**

### **How to get my public IP**

I want to connect my website to the world to see so I need to get my public IP address to get my website on the WAN :

```Website to get your public IP address
https://mon-ip.com
```

### **Set a A record with my public IP**

Now that I have my public IP address I will connect to my domain name supplier and create a A record :

```Creation of a A record
Subdomain watermelon
TTL default
Target my public IP address
```

### **Modification of the vHost & port.conf**

After that I create my A record I will modified some parameters in my vHost and my port.conf files :

```vHost part
<VirtualHost *:2040>
    ServerName watermelon.cybercat.com
    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # Logs
    ErrorLog ${APACHE_LOG_DIR}/wordpress-error.log
    CustomLog ${APACHE_LOG_DIR}/wordpress-access.log combined

</VirtualHost>
```
In the port.conf file I specified the new port that I have open on my FW.

```Port.conf part
Listen 2040
```

### **Add the domaine name & port on WordPress**

Then I will specified the full url + port on the WordPress settings :

* Settings
  * General
    * WordPress Web Address
      * http://watermelon.cybercat.com:2040
* Settings
  * General
    * WordPress Site Web Address
      * http://watermelon.cybercat.com:2040
  

## **HTTPS**

### **Cerbot & Let's Encript certificate**

For the final part I will make a SSL certificate for my website using Cerbot :

```Cerbot installation
sudo apt -y install python3-certbot-apache
```

**&**

```Activation of Cerbot
sudo certbot --apache -d watermelon.cybercat.com
```
