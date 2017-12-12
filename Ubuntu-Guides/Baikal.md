# Introduction
We are going to install Baikal that's a light and good DAV server, it's greate to use with Seafile.<br>
If you want to read more please read, http://sabre.io/baikal/ <br>
<br>
If you want to run Baikal inside the same WAN IP as your other servers, take Seafile for a example then you need to setup a NGINX Reverse proxy server in a VM.<br>
Guide for that are comming soon.<br>
<br>
Before you start to install Baikal I do recommend that you are following the guides that are listed below:
* How to secure your Ubuntu server <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Secure-Server.md
* How to setup Hyper-V in the right way<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Run-Hyper-V.md
* How to install and setup SSMTP<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```

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
We need to change two thing in the configuration file after we have installed NGINX.
```
sudo nano /etc/nginx/nginx.conf
```
Then we need to replace this line:
```
user  nginx;
```
With this line:
```
user  www-data;
```
And also we need to replace this line:
```
include /etc/nginx/conf.d/*.conf;
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
#### Optimize NGINX
Please see this link for instructions how to optimize NGINX.<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/NGINX-Optimize.md <br>
#### Secure NGINX with Fail2Ban
This configuration are adapted to work with NGINX, please follow this link.<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/NGINX-fail2ban.md
#### Configure NGINX
First we need to delete the old / default configurations.
Just so your aware of it, NGINX are sensetiv how you are writing the configuration file with spaces etc.
```
sudo rm -rf /etc/nginx/conf.d/default.conf
sudo rm -rf /etc/nginx/sites-enabled/default
sudo rm -rf /etc/nginx/sites-available/default
```
Then we need to create the file that we are going to use.
```
sudo nano /etc/nginx/sites-available/dav.conf
```
And copy this in to the file, and remember to replace the dav.example.org to your own domain and also change the path for the location folder in the bottom the yours.
```
server {
  listen       80;
  server_name  dav.example.org;

  root  /var/www/baikal/html;
  index index.php;

  rewrite ^/.well-known/caldav /dav.php redirect;
  rewrite ^/.well-known/carddav /dav.php redirect;

  charset utf-8;

  location ~ /(\.ht|Core|Specific) {
    deny all;
    return 404;
  }

  location ~ ^(.+\.php)(.*)$ {
    try_files $fastcgi_script_name =404;
    include        /etc/nginx/fastcgi_params;
    fastcgi_split_path_info  ^(.+\.php)(.*)$;
    fastcgi_pass   unix:/run/php/php7.0-fpm.sock;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    fastcgi_param  PATH_INFO        $fastcgi_path_info;
  }
}
```
Now we are going to activate our configuration file.
```
sudo ln -s /etc/nginx/sites-available/dav.conf /etc/nginx/sites-enabled/dav.conf
```
Now we are almoste done, we just need to restart NGINX.
```
sudo service nginx reload
sudo service nginx restart
```
### Download and install Baikal
Now we are on the last step! We just need to download and install Baikal.<br>
Make sure that you have the latest release of Baikal when I'm writting this guide it's v0.4.6.<br>
Take a look here so it's the latest: https://github.com/sabre-io/Baikal/releases
```
cd /var/www
sudo wget https://github.com/sabre-io/Baikal/releases/download/0.4.6/baikal-0.4.6.zip
```
Then we need to unzip all the files.
```
sudo unzip baikal-0.4.6.zip
```
Now we need to change the premission on the baikal folder.
```
sudo chown -R www-data:www-data /var/www/baikal
```
Now we are done!
#### Installation
To install Baikal just visit the follwoing link in your webbrowser, replace YOURSERVERIP with your IP to the server: http://YOURSERVERIP/admin/install/

During the installation you will first get prompted to write a administration password do that.<br>
When your on the database section of the installation you should have the following settings.<br>
* Check the "Use mysql" box
* MySQL host: localhost
* MySQL database name: baikal-db
* MySQL username: baikal
* MySQL password: (Write the password that you did choose when you did create the user baikal in MariaDB)
