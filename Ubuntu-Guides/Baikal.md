## Introduction
We are going to install Baikal that's a light and good DAV server, it's greate to use with Seafile.<br>
If you want to read more please read, http://sabre.io/baikal/ <br>
<br>
If you want to run Baikal inside the same WAN IP as your other servers, take Seafile for a example then you need to setup a NGINX Reverse proxy server in a VM.<br>
Guide for that are comming soon.<br>
<br>
Before you start to install Baikal I do recommend that you are following the guides that are listed below:
* How to secure your Ubuntu server <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Secure-Server.md
* How to setup Hyper-V right<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Run-Hyper-V.md
* How to install and setup SSMTP<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md

## Installation
First we need to make sure that Ubuntu are up to date.
```
sudo apt-get update && sudo apt-get upgrade -y
```
Then we need to install some things.
```
sudo apt-get install php php-mysql unzip nginx -y
```

### Install MariaDB
We are going to use MariaDB and what we want to do is that we want to run the latest stable version to do that we need to change some files and do some installation.
Run the following commands.
```
sudo apt-get install software-properties-common
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
sudo add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://ftp.ddg.lth.se/mariadb/repo/10.2/ubuntu xenial main'
```
As you can se when I write this document the latest stable version of MariaDB is 10.2, to make sure that it's still the latest version please visit http://downloads.mariadb.org/mariadb/repositories/ 

Now it's time to install MariaDB.
```
sudo apt-get update
sudo apt-get install mariadb-server -y
```
And now to finish the MariaDB setup we need to run some security, this is something that many people are missing and it's really important to do this beacuse otherwise it's easy to get hacked.
```
sudo mysql_secure_installation
```
(If you using auth_socket in MariaDB you just press enter when it's asking for the password, if your not using it then type your root password for MariaDB.)
This setup are really easy to do, now you will be asked if you want to change your administration password for MairaDB here you can choose whatever you want. But for the other questions you should answer Y on everyone of them.

#### Create the databases
Now we need to add some databases to MariaDB.
```
sudo mysql -u root -p
```
(If you using auth_socket in MariaDB you just press enter when it's asking for the password, if your not using it then type your root password for MariaDB.)
Now we are in MariaDB and we need to create the databases for Baikal.
```
create database `baikal-db`;
```
Now we need to create the Baikal user for MariaDB and give it access to the databases that we have been creating.
Change the "identified by 'baikal';" to a password of your choosing rather then baikal use a strong and long password, you should write your password inside the '' signs and make sure not to delete them or the ; sign.
```
create user 'baikal'@'localhost' identified by 'baikal';
 
GRANT ALL PRIVILEGES ON `baikal-db`.* to `baikal`@localhost;
```
Now we are all done, so just do the following.
```
flush privileges;
quit;
```

### Install NGINX
Now we are going to update and setup NGINX. <br>
We need to add some rows to the /etc/apt/sources.list.
```
sudo nano /etc/apt/sources.list
```
Then add this to the file.
```
deb http://nginx.org/packages/ubuntu/ xenial nginx
# deb-src http://nginx.org/packages/ubuntu/ xenial nginx
```
Now we need to check that everything works in the update.
```
sudo apt-get update
```
If a W: GPG error: http://nginx.org/packages/ubuntu xenial Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY $key is encountered during the NGINX repository update, execute the following:
```
## Replace $key with the corresponding $key from your GPG error.
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key
sudo apt-get update
sudo apt-get install nginx
```
Now we can run the installation, during the installation if it asks you if you want to replace a file, choose the default answer and it should be N as in no.
```
sudo apt-get install nginx
```
We need to change one thing in the configuration file after we have installed NGINX.
```
sudo nano /etc/nginx/nginx.conf
```
Now we need to create some folders.
```
sudo mkdir /etc/nginx/sites-enabled
sudo mkdir /etc/nginx/sites-available
```
Then we need to replace this line:
```
include /etc/nginx/conf.d/*.conf
```
With this line:
```
include /etc/nginx/sites-enabled/*.conf;
```
Then just restart and reload NGINX, then we are done with the update.
```
sudo service nginx restart
sudo service nginx reload
```


make sure that you have the latest release, take a look.
https://github.com/sabre-io/Baikal/releases
then go to
cd /var/www
download the rls
wget https://github.com/sabre-io/Baikal/releases/download/0.4.6/baikal-0.4.6.zip

sudo chown -R www-data:www-data /var/www/baikal


sudo nano /etc/php/7.0/fpm/pool.d/www.conf

change premission on /var/run/php/php7.0-fpm.sock should be user nginx
