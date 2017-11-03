# Introduction
I'll guide you to how you can install Seafile on a Ubuntu 16.04 LTS machine, in this document I'll only write the best practices.
I'll keep this document up to date as much as I can.
To tighten up your security on your Ubuntu server please se the NohaTech-Security-Ubuntu.md file.

# Installation
Now we going to start the installation.

### Update Ubuntu
We need to make sure that Ubuntu are up to date, use this command below to do that.
```
 sudo apt-get update && sudo apt-get upgrade -y
```
Now reboot your server

### Install MariaDB
We are going to use MariaDB and what we want to do is that we want to run the latest stable version to do that we need to make some changes.
```
 sudo apt-get install software-properties-common
 sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
```
And then we need to add some lines to /etc/apt/sources.list
```
 sudo nano /etc/apt/sources.list
```
Then add this two lines in the bottom of the sources.list
```
# http://downloads.mariadb.org/mariadb/repositories/
deb [arch=amd64,i386] http://ftp.ddg.lth.se/mariadb/repo/10.2/ubuntu xenial main
deb-src http://ftp.ddg.lth.se/mariadb/repo/10.2/ubuntu xenial main
```
As you can se when I write this document the latest stable version of MariaDB is 10.2, to make sure that it's still the latest version please visit http://downloads.mariadb.org/mariadb/repositories/ 

Now it's time to install MariaDB.
```
 sudo apt-get update
 sudo apt-get install mariadb-server -y
```
And now to finish the MariaDB setup we need to run some security, this is something that many people are missing and it's really important to do this beacuse othervise it's easy to get attacked.
```
 sudo mysql_secure_installation
```
(If you using auth_socket in MariaDB you just press enter when it's asking for the password, if your not using it then type your root password for MariaDB.)
This setup are relly easy to do, now you will be asked if you want to change your administration password for MairaDB here you can choose whatevery you want. But for the other quastions you just answer Y.

#### Create the databases
Now we need to add some databases to MariaDB, we are going to do that manually because of the new unix_socket auth that MariaDB have been start using, because of that the "setup-seafile-mysql.sh" will not work depending of what version of MariaDB your running - so better safe then sorry.
```
 sudo mysql -u root -p
```
(If you using auth_socket in MariaDB you just press enter when it's asking for the password, if your not using it then type your root password for MariaDB.)
Now we are in MariaDB and we need to create the databases.
```
 create database `ccnet-db` character set = 'utf8';
 create database `seafile-db` character set = 'utf8';
 create database `seahub-db` character set = 'utf8';
```
Now we need to create the Seafile user for MariaDB and give it access to the databases that we have been creating.
Change the "identified by 'seafile';" to a password of your choosing rather then seafile use a strong and long password, you should write your password inside the '' signs and make sure not to delete them or the ; sign.
```
 create user 'seafile'@'localhost' identified by 'seafile';
 
GRANT ALL PRIVILEGES ON `ccnet-db`.* to `seafile`@localhost;
GRANT ALL PRIVILEGES ON `seafile-db`.* to `seafile`@localhost;
GRANT ALL PRIVILEGES ON `seahub-db`.* to `seafile`@localhost;
```
Now we are all done, so just do the following.
```
 flush privileges;
 quit;
```


### Download Seafile
Now it's time to download Seafile. As you can see in this example I'm using the version 6.2.2 change the filenames to the ones that you have.
```
 wget https://download.seadrive.org/seafile-server_6.2.2_x86-64.tar.gz
```
And now we need to unpack it.
```
 sudo dpkg -i seafile-server_6.2.2_x86-64.tar.gz
```
And now we need to create some folders, change the "nohatech" folders name to a name that you prefer.
```
 mkdir nohatech
 mkdir nohatech/installed
```
Now we need to move the seafile-server_6.2.2 folder and the seafile-server_6.2.2_x86-64.tar.gz file.
```
 mv seafile-server_6.2.2_x86-64.tar.gz nohatech/installed
 mv seafile-server_6.2.2 nohatech
```
