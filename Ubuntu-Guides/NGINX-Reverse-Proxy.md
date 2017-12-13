
# Introduction
If you want multiple servers over the HTTP and HTTPS port and you only have one (WAN)(INTERNET) IP you need a reverse proxy that can route the traffic to the correct server. The good thing here is that you can have everything on the reverse proxy, the protection and also the SSL certs etc. You can host this server on it's own machine or simpley on a VM.

Before you start to setup the reverse proxy I do recommend that you are following the guides that are listed below:
* How to secure your Ubuntu server <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Secure-Server.md
* How to setup Hyper-V in the right way<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Run-Hyper-V.md
* How to install and setup SSMTP<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document

ctrl+w = you can search in the document.
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```
## Firewall
You need to open port 80/tcp and port 443/tcp in UFW if you have that activated, and also you need to portforward port 80/tcp and 443/tcp from your gateway (router) to the machine or VM that are going to be the Reverse proxy.

## Install NGINX
First we need to install NGINX, this is important as we need some of this files before we update NGINX.
```
sudo apt-get install nginx -y
```
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
include /etc/nginx/conf.d/*.conf;
```
With this line:
```
include /etc/nginx/sites-enabled/*.conf;
```
And now we are going to delete some configuration files that we don't need anymore.
```
sudo rm -rf /etc/nginx/conf.d/default.conf
sudo rm -rf /etc/nginx/sites-enabled/default
sudo rm -rf /etc/nginx/sites-available/default
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
#### Create SSL self signed.
You are only going to need to do this one time, this is because we need this for the Let's Encrypt later.
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
#### Create dhparam file.
You need to do this for every site that your going to have this reverse proxy for.
Now we need to create the dhparam.pem file, it's a little tricky we need to create it in our home folder then move it to the /etc/ssl/private/ folder.
I recommend that your naming it like this dhparam_test.pem but replace "test" with the name of the site that you want to route.
Make sure that you also change the "test" in the commands later on.
```
cd ~
sudo openssl dhparam 2048 > dhparam_test.pem
sudo mv dhparam_test.pem /etc/ssl/private/dhparam_test.pem
```
#### First configuration file.
For every site we want to route we need to create a configuration file, so if you have three sites you need to do this step three times.
So now we need to create the configuration file, it's a good ide to name it to the same name as the server that you want to route to.
```
sudo nano /etc/nginx/sites-available/test.conf
```
This is one example for the configuration, make sure that you have changed the text that has a # sign after to your own text in this file.
```
    server {
        listen       80;
        server_name  test.example.se; # Write your own domain name here.
        rewrite ^ https://$http_host$request_uri? permanent;   
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin" always;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
        server_tokens off;
    }
    server {
        listen 443;
        ssl on;
        ssl_certificate /etc/ssl/private/cacert.pem;        # KEEP THIS, we will replace this later efter we have done Let's Encrypt
        ssl_certificate_key /etc/ssl/private/privkey.pem;   # KEEP THIS, we will replace this later efter we have done Let's Encrypt
        server_name test.example.se;
        ssl_session_timeout 5m;
        ssl_session_cache shared:SSL:5m;

        # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
        ssl_dhparam /etc/ssl/private/dhparam_test.pem; # make sure that you have changed the dhparam_test.pem to your own name.

        # secure settings (A+ at SSL Labs ssltest at time of writing)
        # see https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256$
        ssl_prefer_server_ciphers on;

        proxy_set_header X-Forwarded-For $remote_addr;

        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin" always;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
        server_tokens off;

        location / {
            proxy_pass         http://192.168.0.23; # Change this to the right IP to the server you want to route to.
            proxy_set_header   Host $host;
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Host $server_name;
            proxy_set_header   X-Forwarded-Proto https;
            proxy_request_buffering off;

            access_log      /var/log/nginx/test.access.log; # Re-name "test" to the name for the server that your routing to.
            error_log       /var/log/nginx/test.error.log;  # Re-name "test" to the name for the server that your routing to.

            proxy_read_timeout  1200s;

            client_max_body_size 0;
        }
        location '/.well-known/acme-challenge' {
          default_type "text/plain";
          root /mnt/certbot-webroot;
        }
    }
```
Now we need to activate the configuration file, remember to replace test.conf with your own text.
```
sudo ln -s /etc/nginx/sites-available/test.conf /etc/nginx/sites-enabled/test.conf
```
And now we just need to restart NGINX and after that we are done.
```
sudo service nginx restart
sudo service nginx reload
```
Now if you visiting your domain it will be re-routed to the right server.
