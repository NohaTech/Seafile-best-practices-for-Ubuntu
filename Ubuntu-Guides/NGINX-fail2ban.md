# Introduction
As I have written many guides, I need to save some time. So I have made this document seperated so I can just link to it from the guides to save time and also when I need to change something I can just do it in one document.<br>
What we are going to do here is that we are going to secure NGINX with Fail2Ban filters that are adopted for NGINX.
I also writting the Fail2Ban filter for SSH in this guide as we all want to protect our SSH.

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```
# Installation

Fail2Ban are searching trough your logfiles and are blocking IP's that are trying to hack your server in different ways.
So now we are going to install Fail2Ban
```
sudo apt-get install fail2ban -y
```
### Latest version
As always we are using the latest stable version of the software and Fail2Ban is no different, but as version 10 have some different rules etc. we will still be at version 9 as that's the most stable one.
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
### Configuration
I'll just past the configuration here that works with the filters that we have done and the best settings for the filter, if you want to se more information regarding the configuration file you can read Secure-server.md. <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Secure-Server.md

If you want to activate so you can send mails from Fail2Ban you need to follow this guide first Install-ssmtp.md. <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md

Now we are going to create the configuration file.
```
sudo nano /etc/fail2ban/jail.local
```
Then add this to the file.
```
[DEFAULT]

# Also add your gateways IP numbere here.
ignoreip = 127.0.0.1/8
bantime = 259200
findtime = 3600
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

[nginx-http-auth]

enabled  = true
port     = https,http
filter   = nginx-http-auth
logpath  = /var/log/nginx/*error.log

[nginx-badbots]

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

[nginx-botsearch]

enabled  = true
port     = http,https
filter   = nginx-botsearch
logpath  = /var/log/nginx/*error.log
```
Now we are all done, we just need to restart Fail2Ban.
```
sudo service fail2ban restart
```
