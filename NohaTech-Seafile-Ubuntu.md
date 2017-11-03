# Introduction
I'll guide you to how you can install Seafile on a Ubuntu 16.04 LTS machine, in this document I'll only write the best practices.
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
This setup are relly easy to do, now you will be asked if you want to change your administration password for MairaDB here you can choose whatevery you want. But for the other quastions you just answer Y.
