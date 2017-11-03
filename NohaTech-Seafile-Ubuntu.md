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

### The last installation
Now it's time to do the installation of the rest of the needed things, as you can see it's deverted in to blocks so run one command at the time to make sure that everything are installing correctly.
```
 sudo apt-get install python -y
 
 sudo apt-get install python2.7 libpython2.7 python-setuptools python-imaging python-ldap python-urllib3 ffmpeg python-pip python-mysqldb python-memcache memcached libmemcached-dev zlib1g-dev -y
 
 sudo -H pip install pillow moviepy pylibmc django-pylibmc
```
You will be prompted to update pip, so we are going to do that - if you don't get prompted about it you can still run this commands.
```
 sudo -H pip install --upgrade pip
```
And now we need to update Pillow to the latest to have the best experience with thumbnails and viewing our pictures from the webbrowser.
```
 sudo -H pip install --upgrade Pillow
```

# Download and setup Seafile
In this section we are going to download, install and setup Seafile.

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

### Install Seafile
Now it's time to run the setup script for Seafile.
```
 cd nohatech/seafile-server_6.2.2
 
 ./setup-seafile-mysql.sh
```
During the setup you should choose
```
 [2] Use existing ccnet/seafile/seahub databases
```
And then read everything in most cases it's just run at default except when it asks you for your username and password for the databases then you should write the ones that you did choose in the MariaDB installation above.
The setup script will ask you the names of the databases, and it's the same as the one you did choose in the MariaDB installation also, if you don't rememeber what you did choose take a look at the MariaDB installation section above.

Now you should have new folders in the nohatech/ folder.
So what we need to do now is to run Seafile for the first time and then you will be prompted to create the Administrator when you do run the ./seahub.sh start command, but it's importent that you are running the ./seafile.sh start before the seahub as both are needed to run Seafile.
```
 cd nohatech/seafile-server-latest
 
 ./seafile.sh start
 ./seahub.sh start
```

Now the things are up an running for you, good! What we need to do now is to do some modifications and install some add-ons to make it run perfectly.
But you can try to login on it trough your webbrowser: http://yourlocalip:8000/
Make sure that you have opend port 8000 in the UFW firewall if you have it activated.

### Add Memcached
It's recommended to setup Memcached for Sefile to increase the performance, we have aldready installed all of the necessary component.
So what we need to do is to add some lines to the seahub_settings.py file.
```
 nano nohatech/conf/seahub_settings.py
```
Then add the following lines to the file.
```
 CACHES = {
    'default': {
        'BACKEND': 'django_pylibmc.memcached.PyLibMCCache',
        'LOCATION': '127.0.0.1:11211',
    }
 }
```

### Install NGINX
We need NGINX so we can access Seafile trough 443 port and use SSL.

# UFW Firewall

# Fail2Ban

### For Seafile

### For NGINX
