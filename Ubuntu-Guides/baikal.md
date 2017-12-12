## Introduction
We are going to install Baikal that's a light and good DAV server, it's greate to use with Seafile.
If you want to read more please read, http://sabre.io/baikal/

First we need to install some things.
```
sudo apt-get install php php-mysql -y
```
Then we need to install and setup MariaDB.

mariadb

make sure that you have the latest release, take a look.
https://github.com/sabre-io/Baikal/releases
then go to
cd /var/www
download the rls
wget https://github.com/sabre-io/Baikal/releases/download/0.4.6/baikal-0.4.6.zip

sudo chown -R www-data:www-data /var/www/baikal


sudo nano /etc/php/7.0/fpm/pool.d/www.conf

change premission on /var/run/php/php7.0-fpm.sock should be user nginx
