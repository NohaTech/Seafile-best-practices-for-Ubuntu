
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
# Install NGINX
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
