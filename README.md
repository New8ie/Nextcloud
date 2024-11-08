Nextcloud
===
<!--rehype:style=font-size: 38px; border-bottom: 0; display: flex; min-height: 260px; align-items: center; justify-content: center;-->

Nextcloud is a great application for your personal cloud. It will be not functioning when it is error or slow.  this is a installation setup and optimized for performance on your home 
server!

* PHP 8.3 is out.  Nextcloud currently will NOT run on php8.3.  Many required php packages for php8.3 are not out yet, such as intl, mysql, curl, and others*
* This guide has been updated to specify installing php 8.2 packages, be sure you are running php8.2!*
* Nextcloud version 30 


# Debian 12 Setup installation
```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install software-properties-common
sudo apt-get install apache2 php8.2-fpm
```
Create File nextcloud.conf
```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```
Paste in the code from here:

(https://github.com/New8ie/Nextcloud/blob/3b237b1bf250ca77921f3b36ba8fb2309db92f81/Apache/nextcloud.conf)

# Enable PHP Config & Add Exention
```bash
sudo a2enconf php8.2-fpm
sudo a2ensite nextcloud.conf
sudo apt-get install imagemagick php8.2-imagick memcached libmemcached-tools php8.2-memcached php8.2-apcu mariadb-server php8.2-gd php8.2-mysql php8.2-curl php8.2-mbstring php8.2-intl php8.2-gmp php8.2-bcmath php8.2-xml php8.2-zip unzip smbclient
sudo a2enmod headers rewrite mpm_event http2 mime proxy proxy_fcgi setenvif alias dir env ssl proxy_http proxy_wstunnel
sudo a2dismod mpm_prefork
```
# Edit file memcached.conf
```bash
sudo nano /etc/memcached.conf 
```
  * memory value 64 to 2048

# Edit file www.conf
```bash
sudo nano /etc/php/8.2/fpm/pool.d/www.conf
```  
  * max_children = 80
  * start_servers = 20
  * min_spare_servers = 20
  * max_spare_servers = 60
 uncomment these lines:
  * env[HOSTNAME] = $HOSTNAME
  * env[PATH] = /usr/local/bin:/usr/bin:/bin
  * env[TMP] = /tmp
  * env[TMPDIR] = /tmp
  * env[TEMP] = /tmp

# Edit file php.ini
```bash
sudo nano /etc/php/8.2/fpm/php.ini
```
* memory_limit = 2048M
* post_max_size = 512M
* upload_max_filesize = 10240M
down in opcache settings:
* opcache.enable=1
* opcache.memory_consumption=2048
* opcache.interned_strings_buffer=64
* opcache.max_accelerated_files=150000
* opcache.max_wasted_percentage=15
* opcache.revalidate_freq=60
* opcache.save_comments=1
* opcache.jit=1255
* opcache.jit_buffer_size=256M

# Setup MariaDB
```bash
sudo apt install mariadb-server
sudo systemctl is-enabled mariadb
sudo mysql_secure_installation
```
* Create a new database and user for Nextcloud. Log in to MariaDB using the following command:
```bash
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'changeme';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'username'@'localhost';
FLUSH PRIVILEGES;
```
- Check privileges user MariaDB 
```bash
SHOW GRANTS FOR 'admin'@'localhost';
```

# Downloading Nextcloud Source Code
```bash
cd /var/www
sudo wget https://download.nextcloud.com/server/releases/latest.zip
sudo unzip latest.zip
sudo chown -R www-data:www-data /var/www/nextcloud
sudo systemctl restart apache2
sudo systemctl restart memcached
sudo systemctl restart php8.2-fpm
```
Open website and continue nextcloud isntallation on new website

# Edit file Config.php
```bash
sudo nano /var/www/nextcloud/config/config.php
```
Grab the code to add from here:
https:/

just in case
```bash
sudo systemctl restart apache2 
```

Your nextcloud should now be running optimally!

*Optional but highly recommended*

Enable OCC -- nextcloud command line function
```bash
sudo nano /etc/php/8.2/mods-available/apcu.ini
```
* add the following line at the bottom:
apc.enable_cli=1

* in nextcloud go to
administration settings  -- basic settings -- change from Ajax to Cron (recommended)

if you want to test if cron is working
```bash
sudo -u www-data php -f /var/www/nextcloud/cron.php
```
if you see nothing it is working, if you get an error it is not working
