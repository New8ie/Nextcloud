# Nextloud

Nextcloud is a great system for setting up your personal cloud and sharing with friends.  It can be frustrating when it is slow.  Let's get nextcloud entirely setup and optimized for performance on your server!

* PHP 8.3 is out.  Nextcloud currently will NOT run on php8.3.  Many required php packages for php8.3 are not out yet, such as intl, mysql, curl, and others*
* This guide has been updated to specify installing php 8.2 packages, be sure you are running php8.2!*
* Nextcloud 30 is out, it also wants you to install bz2 php extension*
* sudo apt-get install php8.2-bz2*


# Server Debian 12 Setup 

- sudo apt-get update
- sudo apt-get upgrade
- sudo apt-get install software-properties-common
- sudo apt-get install apache2 php8.2-fpm

Create File nextcloud.conf

- sudo nano /etc/apache2/sites-available/nextcloud.conf

paste in the code from here:
(https://github.com/New8ie/nextcloud/)

# Enable PHP Config & Add Exention

- sudo a2enconf php8.2-fpm
- sudo a2ensite nextcloud.conf
- sudo apt-get install imagemagick php8.2-imagick memcached libmemcached-tools php8.2-memcached php8.2-apcu mariadb-server php8.2-gd php8.2-mysql php8.2-curl php8.2-mbstring php8.2-intl php8.2-gmp php8.2-bcmath php8.2-xml php8.2-zip unzip smbclient
- sudo a2enmod headers rewrite mpm_event http2 mime proxy proxy_fcgi setenvif alias dir env ssl proxy_http proxy_wstunnel
- sudo a2dismod mpm_prefork

sudo nano /etc/memcached.conf
change 64 to 1024

save and exit

sudo nano /etc/php/8.2/fpm/pool.d/www.conf
max_children = 80
start_servers = 20
min_spare_servers = 20
max_spare_servers = 60

uncomment these lines:
env[HOSTNAME] = $HOSTNAME
env[PATH] = /usr/local/bin:/usr/bin:/bin
env[TMP] = /tmp
env[TMPDIR] = /tmp
env[TEMP] = /tmp

save and exit

sudo nano /etc/php/8.2/fpm/php.ini

memory_limit = 1024M
post_max_size = 512M
upload_max_filesize = 1024M

down in opcache settings:
opcache.enable=1
opcache.memory_consumption=1024
opcache.interned_strings_buffer=64
opcache.max_accelerated_files=150000
opcache.max_wasted_percentage=15
opcache.revalidate_freq=60
opcache.save_comments=1
opcache.jit=1255
opcache.jit_buffer_size=256M

save and exit

setup MariaDB
sudo mysql

CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'username'@'localhost';
FLUSH PRIVILEGES;

quit;

cd /var/www

sudo wget https://download.nextcloud.com/server...
sudo unzip nextcloud-27.1.4.zip
sudo chown -R www-data:www-data /var/www/nextcloud

sudo systemctl restart apache2
sudo systemctl restart memcached
sudo systemctl restart php8.2-fpm

open website and continue nextcloud isntallation on new website

sudo nano /var/www/nextcloud/config/config.php

grab the code to add from here:
https://github.com/jhodak/linux-confi...

save and exit

sudo systemctl restart apache2 just in case.

Your nextcloud should now be running optimally!

*Optional but highly recommended*
Enable OCC -- nextcloud command line function

sudo nano /etc/php/8.2/mods-available/apcu.ini

add the following line at the bottom:
apc.enable_cli=1

save and exit

in nextcloud go to
administration settings  -- basic settings -- change from Ajax to Cron (recommended)

if you want to test if cron is working
sudo -u www-data php -f /var/www/nextcloud/cron.php

if you see nothing it is working, if you get an error it is not working
