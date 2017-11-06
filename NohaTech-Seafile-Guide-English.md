# Introduction
I'll guide you trough how you can install Seafile on a Ubuntu 16.04 LTS machine, in this document I'll only adapt best practices.
This guide is for the CE version of Seafile, the free version. But you can use it for the PRO version also but then it's some additional steps that I have not included in this guide.
To tighten up your security on your Ubuntu server please see the NohaTech-Ubuntu-Secure-Server.md file.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-Secure-Server-English.md

If you are going to run Seafile on a Hyper-V VM, please read NohaTech-Ubuntu-Hyper-V-English.md to optimize it and also adopte it to work with Ubuntu.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-Hyper-V-English.md

We will use a texteditor called nano in this guide, so here is some commands that you need to know.
```
 ctrl+k = deletes a hole row in the document
 
 ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```

***All the path's in this guide are starting at /opt/nohatech/ make sure that you change them to the path that your using.***

# Installation
Now we going to start the installation.

### Create user
Best is to have a "clean" Ubuntu installation to install Seafile on, but if you don't have a clean one it's recommended that Seafile are running with it's own user - ***you should not run Seafile with root user or sudo command.***

### Update Ubuntu
We need to make sure that Ubuntu are up to date, use this command below to do that.
```
 sudo apt-get update && sudo apt-get upgrade -y
```
Now reboot your server

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
This setup are relly easy to do, now you will be asked if you want to change your administration password for MairaDB here you can choose whatevery you want. But for the other quastions you should answer Y on everyone of them.

#### Create the databases
Now we need to add some databases to MariaDB, we are going to do that manually because of the new unix_socket auth that MariaDB have been start using, because of that the "setup-seafile-mysql.sh" will not work depending of what version of MariaDB your running - so better safe then sorry.
```
 sudo mysql -u root -p
```
(If you using auth_socket in MariaDB you just press enter when it's asking for the password, if your not using it then type your root password for MariaDB.)
Now we are in MariaDB and we need to create the databases for Seafile.
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

### The last installation step
Now it's time to do the installation of the rest of the needed things, as you can see it's deverted in to blocks so run one command at the time to make sure that everything are installing correctly.
```
 sudo apt-get install python -y
 
 sudo apt-get install python2.7 libpython2.7 python-setuptools python-imaging python-ldap python-urllib3 ffmpeg python-pip python-mysqldb python-memcache memcached libmemcached-dev zlib1g-dev -y
 
 sudo -H pip install pillow moviepy pylibmc django-pylibmc
```
You will be prompted to upgrade pip during the installations above, so we are going to do that - if you don't get prompted about it you can still run this command to be sure that you have the latest version.
```
 sudo -H pip install --upgrade pip
```
And now we need to upgrade Pillow to the latest version so we can get the best experience with thumbnails and viewing our pictures from the webbrowser.
```
 sudo -H pip install --upgrade Pillow
```
And we can also upgrade this two pip package.
```
 sudo -H pip install --upgrade python-memcached==1.57
 sudo -H pip install --upgrade pytz==2016.7
```

# Download and setup Seafile
In this section we are going to download, install and setup Seafile.
To see what's the latest version of Seafile CE please visit this link: https://www.seafile.com/en/download/
We are going to use the /opt folder to install Seafile in, and we are going to use a folder named "nohatech".
You can change the "nohatech" folder name to what ever you want I just using it as a example.

### Download Seafile
Now it's time to download Seafile. As you can see in this example I'm using the version 6.2.2 change the filenames to the version you are installing.
```
 cd /opt
 wget https://download.seadrive.org/seafile-server_6.2.2_x86-64.tar.gz
```
And now we need to unpack it.
```
 tar -xvzf seafile-server_6.2.2_x86-64.tar.gz
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
 cd /opt/nohatech/seafile-server_6.2.2
 
 ./setup-seafile-mysql.sh
```
During the setup you should choose
```
 [2] Use existing ccnet/seafile/seahub databases
```
And then read everything in most cases it's just run at default except when it asks you for your username and password for the databases then you should write the ones that you did choose in the MariaDB installation step above.
The setup script will ask you the names of the databases, and it's the same as the one you did create in the MariaDB installation also, if you don't rememeber what you did choose take a look at the MariaDB installation section above.

Now you should have new folders in the nohatech/ folder.
So what we need to do now is to run Seafile for the first time and then you will be prompted to create the Administrator when you do run the ./seahub.sh start command, but it's importent that you are running the ./seafile.sh start command before the seahub as both are needed to run Seafile.
And also remember that you should never start Seafile with the sudo command or change any of the files in your Seafile folder with the sudo command.
```
 cd /opt/nohatech/seafile-server-latest
 
 ./seafile.sh start
 ./seahub.sh start
```
Now things are up an running for you, good! What we need to do now is to do some modifications and install some add-ons to make it run perfectly.
But you can try to login on it trough your webbrowser: http://yourlocalip:8000/
Make sure that you have opend port 8000 in the UFW firewall if you have it activated, but UFW should be disable as default.

### Add Memcached
It's recommended to setup Memcached for Sefile to increase the performance, we have already installed all of the necessary components.
So what we need to do is to add some lines to the seahub_settings.py file.
```
 nano /opt/nohatech/conf/seahub_settings.py
```
Then add the following lines in the bottom of the file.
```
 CACHES = {
    'default': {
        'BACKEND': 'django_pylibmc.memcached.PyLibMCCache',
        'LOCATION': '127.0.0.1:11211',
    }
 }
```
No as we are changing to use Memcached we need to reboot the server.
```
 sudo reboot
```
#### Memcached trough unix_socket
This is recommended but it's not completly tested yet so for now we will just use the http socket option, but I'll update this section as soon as possible, performence should increase with about 30% according to some benchmarks.

### Start Seafile on system boot
We want Seafile to autostart on boot of our server, so now we going to fixa that.
So now we need to create some new files.
Remember to change the path to your own path for Seafile also remember to change the user and group name if your user and group name are not Seafile as in this example.
```
 sudo nano /etc/systemd/system/seafile.service
```
And then add the following to the file.
```
 [Unit]
 Description=Seafile
 After=network.target mysql.service

 [Service]
 Type=oneshot
 ExecStart=/opt/nohatech/seafile-server-latest/seafile.sh start
 ExecStop=/opt/nohatech/seafile-server-latest/seafile.sh stop
 RemainAfterExit=yes
 User=seafile
 Group=seafile

 [Install]
 WantedBy=multi-user.target
```
Now we need to create a second file.
```
 sudo nano /etc/systemd/system/seahub.service
````
Then add this to the file.
```
 [Unit]
 Description=Seafile hub
 After=network.target seafile.service

 [Service]
 ExecStart=/opt/nohatech/seafile-server-latest/seahub.sh start
 ExecStop=/opt/nohatech/seafile-server-latest/seahub.sh stop
 User=seafile
 Group=seafile
 Type=oneshot
 RemainAfterExit=yes

 [Install]
 WantedBy=multi-user.target
```
And now we are on the last step, now we need to enable this as a service.
```
 sudo systemctl enable seafile.service
 sudo systemctl enable seahub.service
```

### Seafile GC
Seafile GC are going to clean up the deleted files etc. to free space on your server. But on the CE (free version) of Seafile it can't run as long as Seafile are running.
So we are going to make a script and add it to crontab to make it autorun and solve this little issue for us.

First we need to create the file.
```
 nano /opt/nohatech/seafile/cleanupScript.sh
```
Then add the following to the file.
```
 #!/bin/bash
 # stop the server
 echo Stopping the Seafile-Server...
 systemctl stop seafile.service
 systemctl stop seahub.service
 
 echo Giving the server some time to shut down properly....
 sleep 20

 # run the cleanup
 echo Seafile cleanup started...
 sudo -u seafile /opt/nohatech/seafile-server-latest/seaf-gc.sh -r

 echo Giving the server some time....
 sleep 10

 # start the server again
 echo Starting the Seafile-Server...
 systemctl start seafile.service
 systemctl start seahub.service

 echo Seafile cleanup done!
```
Make sure that the script has been given execution rights, to do that run this command
```
 sudo chmod +x /opt/nohatech/seafile/cleanupScript.sh
```
Now we need to add the script to crontab so we can autorun it, you will be asked what editor you want to use choose number 2 (Nano) it's the one that we are using in this guide.
```
 sudo crontab -e
```
Then add the following line at the end on the crontab file.
```
 0 2 * * Sun /opt/nohatech/seafile/cleanupScript.sh
```
This means that every Sunday at 02:00 this script will run.

### Ignore list for Seafile
You can if you want ignore files and folders for sync.
What you need to do is that you need to create a file named seafile-ignore.txt in the library where the files and folders are located that you want to ignore and then the rules for that library are sett, so you need to create this file for every library that you have.
If you want to ignore a folder you can write it like this.
```
 example/
```
If you want to ignore a file you can type it like this.
```
 # ignore the exact file.
 example.ini
```
Ignore list also supports wildecards so you can modify this list in many different ways, but if you want to learn more how to modify the list with wildecards, read this link https://www.seafile.com/en/help/ignore/

### Install NGINX
We need NGINX so we can access Seafile trough 443 port and use SSL also NGINX are going to work as a revers proxy for us.
Since version 6.2* of Seafile FastCGI are not recommended and the support for it will end soon, so we are going to use what's recommended and whats going to be supported in the future, WSGI.
Also you need to open port 80 and 443 in your firewall (UFW) if you have it activated, and if your using a router you also need to portforward this ports to the Seafile machine if you want to access Seafile outside your LAN.
After you have done this your going to access Seafile trough the following address: http://YOURIP/ or https://YOURIP/ depends if your using HTTP (port 80) or HTTPS (SSL over port 443)

Before we start we need to stop Seafile.
```
 cd /opt/nohatech/seafile-server-latest/
 ./seafile.sh stop
 ./seahub.sh stop
```
Now we are ready to go!

#### Install latest stable version
As always we are going to install the latest stabel version, and to do that we need to add some rows to the /etc/apt/sources.list.
```
 sudo nano /etc/apt/sources.list
```
Then add this to the file.
```
 deb http://nginx.org/packages/ubuntu/ xenial nginx
 # deb-src http://nginx.org/packages/ubuntu/ xenial nginx
```
Then we are going to install it.
```
 sudo apt-get update
 sudo apt-get install nginx
```
If a W: GPG error: http://nginx.org/packages/ubuntu xenial Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY $key is encountered during the NGINX repository update, execute the following:
```
 ## Replace $key with the corresponding $key from your GPG error.
 sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key
 sudo apt-get update
 sudo apt-get install nginx
```

#### Configuration
First we need to delete the old / default configurations.
Just so your aware of it, NGINX are sensetiv how you are writing the configuration file with spaces etc.
```
 sudo rm -rf /etc/nginx/sites-enabled/default
 sudo rm -rf rm /etc/nginx/sites-enabled/default
```
Then we need to create the file that we are going to use.
```
 sudo nano /etc/nginx/sites-available/seafile.conf
```
Now we are going to start the configuration, this example file are working you just need to replace the "seafile.example.com" with your own domain. But still, read the hole thing.
```
server {
    listen 80;
    server_name seafile.example.com;

    proxy_set_header X-Forwarded-For $remote_addr;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Frame-Options "DENY" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    server_tokens off;

    location / {
         proxy_pass         http://127.0.0.1:8000;
         proxy_set_header   Host $host;
         proxy_set_header   X-Real-IP $remote_addr;
         proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header   X-Forwarded-Host $server_name;
         proxy_read_timeout  1200s;
         proxy_request_buffering off;

         # used for view/edit office file via Office Online Server
         client_max_body_size 0;

         access_log      /var/log/nginx/seahub.access.log;
         error_log       /var/log/nginx/seahub.error.log;
    }
    location /seafhttp {
        rewrite ^/seafhttp(.*)$ $1 break;
        proxy_pass http://127.0.0.1:8082;
        client_max_body_size 0;

        proxy_connect_timeout  36000s;
        proxy_read_timeout  36000s;
        proxy_send_timeout  36000s;
        proxy_request_buffering off;

        send_timeout  36000s;
    }
    location /media {
        root /opt/nohatech/seafile-server-latest/seahub;
    }
}
```
As we want high security for our site we need to add this lines to every server block in the configuration file, for now we only have one. I have already added these lines in the example config above, but I still want you to know that this lines are not there as default.
```
 add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
 add_header X-Content-Type-Options "nosniff" always;
 add_header X-XSS-Protection "1; mode=block" always;
 add_header X-Frame-Options "DENY" always;
 add_header Referrer-Policy "strict-origin-when-cross-origin" always;
 server_tokens off;
```
Also we don't want NGINX to use a buffer in tmp files so we need to add the following line to the location blocks. I have already added this line in the example config above, but I still want you to know that this lines are not there as default.
```
 proxy_request_buffering off;
```
Also we don't want to set any limitation on the upload size in NGINX as that's something we should do in the seafile.conf file so it's important that you add this line in every "location" block. I have already added this line in the example config above, but I still want you to know that this lines are not there as default.
```
 client_max_body_size 0;
```
Now we are going to activate our configuration file.
```
 sudo ln -s /etc/nginx/sites-available/seafile.conf /etc/nginx/sites-enabled/seafile.conf
```
Now we just need to restart NGINX and then we will have a working NGINX revers proxy over HTTP (port 80) to Seafile. It's recommended that you are also following the Self signed cert guide or even better following the Free SSL cert from Let's Encrypt as it's recommended.
```
 sudo service nginx restart
```
#### Self signed cert
I'm recommending you to use Let's Encrypts SSL but for some reason you might want to use Self signed.
So here's how you do that.

First we need to create privkey.pem and cacert.pem.
```
 sudo openssl genrsa -out /etc/ssl/private/privkey.pem 2048
 sudo openssl req -new -x509 -key /etc/ssl/private/privkey.pem -out /etc/ssl/private/cacert.pem -days 1095
```
If you get any issues when your trying to create the cert's do it this way insted. Replace xxx with your user name. (optional)
```
 cd ~
 sudo openssl genrsa -out /home/xxx/privkey.pem 2048
 sudo openssl req -new -x509 -key /home/xxx/privkey.pem -out /home/xxx/cacert.pem -days 1095
 sudo mv privkey.pem /etc/ssl/private/
 sudo mv cacert.pem /etc/ssl/private/
```
Then we need to create the dhparam.pem file, it's a little tricky we need to create it in our home folder then move it to the /etc/ssl/private/ folder.
```
 cd ~
 sudo openssl dhparam 2048 > dhparam.pem
 sudo mv dhparm.pem /etc/ssl/private/dhparam.pem
```
Now we are done with that, so now we need to start to change the config file to work, I'll add some changes if you want to know what just compare it to the configuration file above, here is a working example of the file, as in the file above you need to replace seafile.example.com with your own domain also the path setting to the location.
I have also added the security settings to this configuration file, if your not are going to use this configuration file, take a look above to see how to adapte the security settings.
```
    server {
        listen       80;
        server_name  seafile.example.com;
        rewrite ^ https://$http_host$request_uri? permanent;    # force redirect http to https
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin-when-cross-origin" always;
        server_tokens off;
    }
    server {
        listen 443;
        ssl on;
        ssl_certificate /etc/ssl/private/cacert.pem;        # path to your cacert.pem
        ssl_certificate_key /etc/ssl/private/privkey.pem;    # path to your privkey.pem
        server_name seafile.example.com;
        ssl_session_timeout 5m;
        ssl_session_cache shared:SSL:5m;

        # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
        ssl_dhparam /etc/ssl/private/dhparam.pem;

        # secure settings (A+ at SSL Labs ssltest at time of writing)
        # see https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256$
        ssl_prefer_server_ciphers on;

        proxy_set_header X-Forwarded-For $remote_addr;

        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin-when-cross-origin" always;
        server_tokens off;

        location / {
            proxy_pass         http://127.0.0.1:8000;
            proxy_set_header   Host $host;
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Host $server_name;
            proxy_set_header   X-Forwarded-Proto https;
            proxy_request_buffering off;


            access_log      /var/log/nginx/seahub.access.log;
            error_log       /var/log/nginx/seahub.error.log;

            proxy_read_timeout  1200s;

            client_max_body_size 0;
        }
#    }
        location /seafhttp {
            rewrite ^/seafhttp(.*)$ $1 break;
            proxy_pass http://127.0.0.1:8082;
            client_max_body_size 0;
            proxy_request_buffering off;
            proxy_connect_timeout  36000s;
            proxy_read_timeout  36000s;
            proxy_send_timeout  36000s;
            send_timeout  36000s;
        }
        location /media {
            root /opt/nohatech/seafile-server-latest/seahub;
        }
    }

```
Now we are almoste done, we just need to restart NGINX.
```
 sudo service nginx restart
```
#### Free SSL cert from Let's Encrypt (recommended)
This is the recommended way to do it, here we will get a free SSL cert that expires after 3 months but we will make a cronjob so we will renew it before it expires.
If you uses this it's going to make your site more secure and also it will show as "trusted" in every webbrowser and every webbrowser can access your site without any issues or warnings.

Above I have been written how to setup NGINX with Self signed cert you can read there how to do the configuration file it's the same but we just need to add and change some things. I'll only write the things you should change or add here the rest you can read above - follow the guide above to the letter and then just change and add the things that I'm writing below.

Then we need to install certbot so we can get the cert's.
```
 sudo apt-get install software-properties-common
 sudo add-apt-repository ppa:certbot/certbot
 sudo apt-get update
 sudo apt-get install python-certbot-nginx
```
Now we need to prepar some things.
First off we need to create a folder.
```
 sudo mkdir /mnt/certbot-webroot
```
Then you should add this to the HTTPS 443 mainserver block.
```
 location '/.well-known/acme-challenge' {
    default_type "text/plain";
    root /mnt/certbot-webroot;
}
```
Now we are all set and can create the certification, change example.se to your domain name, you should not have http or https before it but you can use subdomain.example.se if you want that or example.se.
```
 sudo certbot certonly --webroot -w /mnt/certbot-webroot -d example.se
```
When you did create the cert troug the command above you did get the correct path to your new cert's prompted for you.
Change the following rows so the path is the correct one for you, It should bee something like the path that I have wroten below.
```
 ssl_certificate /etc/letsencrypt/live/example.se/fullchain.pem;  # path to your cacert.pem
 ssl_certificate_key /etc/letsencrypt/live/example.se/privkey.pem;    # path to your privkey.pem
```
Now we are actually done, not so hard right? Well we did do the most of the work before.
So now we just need to restart NGINX.
```
 sudo service nginx restart
```
So now we just need to add this to crontab so the cert will get renewed before it expires.
First we need to look where certbot is located at your server.
```
 whereis certbot
```
Then we need to create the crontab.
```
 sudo crontab -e
```
At the bottom of crontab add this, change the /path/to/certbot so it's matches your path.
```
 47 */12 * * * sleep 16; /path/to/certbot renew --quiet --post-hook "/usr/sbin/service nginx reload"
```

#### Optimize NGINX
So now we want to optimize NGINX for best performence.
First we are going to look how many working processors we have.
```
 grep processor /proc/cpuinfo | wc -l
```
In my case I did get 8.
And now we need to see how many worker connections we can have simultaneously.
```
 ulimit -n
```
In my case I did get 1024.
Now we need to add this new numbers to our NGINX configuration file.
```
 sudo nano sudo nano /etc/nginx/nginx.conf
```
Then we need to change this two rows, and add your number in the end of the lines.
```
 worker_processes 8;
 worker_connections 1024;
```
Now we need to restart NGINX so this changes can take effect.
```
 sudo service nginx restart
```

### Configuration for Seafile
Now we need to make some changes in the config files for Seafile, and remember not to open the files with the sudo command or root user open the files with the user that your using to run Seafile with.
I'll only write what I'm using in my config files, but I'll refer you to a link in every config so you can if you want visit that page to see what more settings you can do. And you should replace NohaTech with your own name.
The following files are located at
```
 cd /opt/nohatech/conf/
```

#### ccnet.conf
```
 nano ccnet.conf
```
Then we need to add the following row.
```
 # Your servername
 USER_NAME = NohaTech
 # Your servername
 NAME = NohaTech
 # The address to your Seafile server, use https if your using SSL (recommended and we are using it in this guide).
 SERVICE_URL = https://example.se
```
For more information see https://manual.seafile.com/config/ccnet-conf.html

#### seafile.conf
```
 nano seafile.conf
```
The we need to add the following row, remember to not delete any rows in the config file just put this rows under the already existing rows in the right [SECTION]
```
 [fileserver]
 # How long time a session can bee open before it times out.
 web_token_expire_time=7200
 [fileserver]
 # Max upload size, it's in MB
 max_upload_size=10000
 [zip]
 # Changes the encoding on the zip download files so it works with Windows.
 windows_encoding = iso-8859-1
 [history]
 # How many days that users can keep there history
 keep_days = 7
 [library_trash]
 # How often trashed libraries are scanned for removal, default 1 day.
 scan_days = 1
 # How many days to keep trashed libraries, default 30 days.
 expire_days = 14
```

For more information see https://manual.seafile.com/config/seafile-conf.html

#### seahub_settings.py
```
 nano seahub_settings.py
```
Then we need to add the following rows.
```
 # Set this to your website/company's name. This is contained in email notificat$
 SITE_NAME = 'NohaTech'
 # Browser tab's title
 SITE_TITLE = 'NohaTech'
 # TimeZone, this is needed to get Fail2Ban to work correctly. Change 'Europe/Stockholm' to the time zone your in.
 TIME_ZONE = 'Europe/Stockholm'
```
Now if we want to send e-mails from Seafile, and it's recommended as links etc. can be sent trough mail we need to put this lines in seahub_settings.py this example are for gmail.
But to make it work you need to setup ssmtp first, to do that follow my other guide NohaTech-Ubuntu-Ssmtp-English.md.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-Ssmtp-English.md
```
 # Email server/settings
 EMAIL_USE_TLS = True
 EMAIL_HOST = 'smtp.gmail.com'
 EMAIL_HOST_USER = 'nohatech@gmail.com'
 EMAIL_HOST_PASSWORD = 'YOURPASSWORD'
 EMAIL_PORT = 587
 DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
 SERVER_EMAIL = EMAIL_HOST_USER

```

For more information see https://manual.seafile.com/config/seahub_settings_py.html

### Seafdav (webdav)
Webdav is let say it so old, it's not Seafiles fault as WebDav is not created by Seafile.
What I do recommend you to use intsted is the SeaDrive client. You can configurate SeaDrive to work as a networkshare and mount it in your OS and work on the files directly from the server.
But if you still want SeafDav (WebDav) activated you can follow this steps.

***I'll do this part when I have time to do it, I'll prioritize other parts of this guide first.***

# UFW Firewall
A Firewall is something that everyone wants and needs these days, so I'm going to guide you trough it.
As default Ubuntu should have UFW installed but if not, then installed it trough this command.
```
 sudo apt-get install ufw
```
The ports that are needed for Seafile to work is 80 and 443, but we also want the SSH port to be opened and it's port 22 as standard, if you have change it by following the NohaTech-Ubuntu-Secure-Server.md document you just change it from 22 to the port number that you have choosed for it.
So let us open the ports.
```
 sudo ufw allow 80/tcp
 sudo ufw allow 443/tcp
 sudo ufw allow 22/tcp
```
And we also need to make some default rules, what we going to do is block all incoming ports that we haven't opend, and open every outgoing port as we want to send traffic and we can't get hacked by the traffic that we are sending out.
```
 sudo ufw default deny incoming
 sudo ufw default allow outgoing
```
And before we going to enable the UFW firewall we need to make sure that every port that we want to have open is open so we don't lock our selfs out.
```
 sudo ufw status numbered
```
We are going to se that port 80,443,22/tcp are open both for IPv4 and IPv6 and that's normal.
Now we can enable the UFW firewall.
```
 sudo ufw enable
```
Now we are finished and you have a firewall installed and activated.

# Fail2Ban
Fail2Ban are searching trough your logfiles and are blocking IP's that are trying to hack your server in differnet ways.
Regarding the Seafile filter for Fail2Ban it's a bug, to make the filter work you need to add the following line to the seahub_settings.py file, if you have been following this guide from the begining you have already done that.
```
 TIME_ZONE = 'Europe/Stockholm'
```
And change it to the timezone that your in.

But to get Fail2Ban to work at all we need to install it first.
```
 sudo apt-get install fail2ban
```
### Latest version
As everything in this document we are using the latest stable version of the software and Fail2Ban is no differnet, but as version 10 have some different rules etc. we will still be at version 9 as that's the most stable one.
So now, let us download and install the newest version.
```
 wget http://se.archive.ubuntu.com/ubuntu/pool/universe/f/fail2ban/fail2ban_0.9.7-2_all.deb
```
then we need to install it.
```
 sudo dpkg -i fail2ban_0.9.7-2_all.deb
```

Now we have the latest version installed and we can continue with the configuration of Fail2Ban.

### Add filters
What we need to do first here is to create the filters that we need, some of them are already created as default in Fail2Ban but some we need to create by our self.

Let us go to the filter.d folder before we begin.
```
 cd /etc/fail2ban/filter.d/
```

First we are going to add a line in a filter.
```
 sudo nano /etc/fail2ban/filter.d/nginx-http-auth.conf
```
And then we need to add this line, add it under the line that almost look the same.
```
 ^ \[error\] \d+#\d+: \*\d+ no user/password was provided for basic authentication, client: <HOST>, server: \S+, request: "\S+ \S+ HTTP/\d+\.\d+", host: "\S+"\s*$
```
Then we need to copy a filter from Apache2 to NGINX.
```
 sudo cp apache-badbots.conf nginx-badbots.conf
```
Then we are going to create our first filter.
```
 sudo nano /etc/fail2ban/filter.d/nginx-forbidden.conf
```
Then add this to the file
```
 [Definition]
 failregex = ^ \[error\] \d+#\d+: .* forbidden .*, client: <HOST>, .*$

 ignoreregex =
```
Create
```
 sudo nano /etc/fail2ban/filter.d/nginx-nohome.conf
```
Add
```
 [Definition]

 failregex = ^<HOST> -.*GET .*/~.*

 ignoreregex =
```
Create
```
 sudo nano /etc/fail2ban/filter.d/nginx-noproxy.conf
```
Add
```
 [Definition]

 failregex = ^<HOST> -.*GET http.*

 ignoreregex =
```
Create
```
 sudo nano /etc/fail2ban/filter.d/nginx-noscript.conf
```
Add
```
 [Definition]

 failregex = ^<HOST> -.*GET.*(\.php|\.asp|\.exe|\.pl|\.cgi|\.scgi)

 ignoreregex =
```
Create
```
 sudo nano /etc/fail2ban/filter.d/seafile-auth.conf
```
Add
```
 # Fail2Ban filter for seafile
 #

 [INCLUDES]

 # Read common prefixes. If any customizations available -- read them from
 # common.local
 before = common.conf

 [Definition]

 _daemon = seaf-server

 failregex = Login attempt limit reached.*, ip: <HOST>

 ignoreregex = 

 # DEV Notes:
 #
 # pattern :     2015-10-20 15:20:32,402 [WARNING] seahub.auth.views:155 login Login attempt limit reached, username: <user>, ip: 1.2.3.4,  attemps: 3
 #        2015-10-20 17:04:32,235 [WARNING] seahub.auth.views:163 login Login attempt limit reached, ip: 1.2.3.4, attempts: 3
```

### Configuration
I'll just past the configuration here that works with the filters that we have done and the best settings for the filter, if you want to se more information regarding the configuration file you can read NohaTech-Ubuntu-Secure-Server-English.md.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-Secure-Server-English.md

If you want to activate so you can send mails from Fail2Ban you need to follow this guide first NohaTech-Ubuntu-Ssmtp-English.md.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-Ssmtp-English.md

And here is a notice regarding the Seafile filter, it's a little different then the others, maxretry set to 1 is actually 3 failed login attempts that's why I have it set to 2 in this guide.

Now we are going to create the configuration file.
```
 sudo nano /etc/fail2ban/jail.local
```
Then add this to the file.
```
[DEFAULT]

# Also add your gateways IP numbere here.
ignoreip = 127.0.0.1/8
bantime = 172800
findtime = 600
maxretry = 0
# Change this to your mail.
destemail = YOUREMAIL
sendername = Fail2Ban
action = %(action_mwl)s

[sshd]

enabled  = true
port     = 2324,22
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 3

[sshd-ddos]

enabled  = true
port     = 2324,22
filter   = sshd-ddos
logpath  = /var/log/auth.log

[seafile]

enabled  = true
port     = https,http
filter   = seafile-auth
logpath  = /home/seafile/nohatech/logs/*seahub.log
bantime  = 3600
maxretry = 2

[nginx-http-auth]

enabled  = true
port     = https,http
filter   = nginx-http-auth
logpath  = /var/log/nginx/*error.log

[nginx-badbots]

enabled  = true
enabled  = true
port     = https,http
filter   = nginx-badbots
logpath  = /var/log/nginx/*access.log

[nginx-nohome]

enabled  = true
port     = https,http
filter   = nginx-nohome
logpath  = /var/log/nginx/*access.log

[nginx-noproxy]

enabled  = true
port     = https,http
filter   = nginx-noproxy
logpath  = /var/log/nginx/*access.log

[nginx-noscript]

enabled  = true
port     = https,http
filter   = nginx-noscript
logpath  = /var/log/nginx/*access.log

[nginx-req-limit]

enabled  = true
port     = https,http
filter   = nginx-limit-req
logpath  = /var/log/nginx/*error.log

[nginx-forbidden]

enabled  = true
port     = https,http
filter   = nginx-forbidden
logpath  = /var/log/nginx/*error.log

```

# Setup security in Seafile Web.
Now we are almoste finished with our setup of the server and we will have a secure Seafile server.
The last thing is to change a little regarding the security.
Login to your Admin account to Seafile troug your browser.
Then go to:
```
 1. Click on profile picture in the upper right corner.
 2. Click on System Admin in the dropdown list.
 3. Click on Settings in the menu to the left.
 4. Scroll down to the following and find the following.
 5. Change the "keep sign in" to 1.
 6. Change "LOGIN_ATTEMPT_LIMIT" to 2.
 7. Make sure that the box are checked in "FREEZE_USER_ON_LOGIN_FAILED".
```
Now we are going to change some things regarding the password rules, we are still on the same page.
```
 1. Check the box for "strong password"
 2. Change "password minimum length" to atleast 8.
 3. "password strength level" should be at 3 but not smaller then 2.
```
This is optional, but you should activate the 2FA for the users that wnat to use it.
To do that just check the box "enable two factor authentication"

# Maintain Seafile
As with everthing you need to Maintain it, and also you might get some issues or something.
I have done a other document for that, NohaTech-Seafile-maintain-English.md

https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Seafile-maintain-English.md
