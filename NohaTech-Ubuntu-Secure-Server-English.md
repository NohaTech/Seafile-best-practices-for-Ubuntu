# Introduction
Well we need to make sure that our server are secure, and here I'll give you some basic things to do to secure your server.
As we are going to go trough many different options you don't need to reboot the server after every thing that we have changed, I have only written it there as if someone just making only that change.
So if you are going to do everything that this document says just reboot the server after you have done everything.

We will use a texteditor called nano in this guide, so here is some commands that you need to know.
```
 ctrl+k = deletes a hole row in the document
 
 ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```

# SSH
We all uses SSH I guess and many boots and script kiddies are trying to attack it. But we can secure it in just a few steps.

***For Fail2ban for SSH see the Fail2Ban section in this document, I have it there***

First we need to open up the config file for SSH.
```
 sudo nano /etc/ssh/sshd_config
```
Then change the following lines.
```
 Protocol 2 (here you should add 2 in the end of the row and delete whatever are written there as default)
 PermitRootLogin no (here you should add no in the end of the row and delete whatever are written there as default)
```
And also you should add the following to the config file.
```
 DebianBanner no
```
Now we need to change the port for SSH, as default SSH uses port 22 and everyone knows that so what we want to do is to change it to some other free port for example 2224 or similer. And remember if you have opend the port 22 in your firewall you need to close that port and open the port that your replacing port 22 with insted.
So, just replace 22 with your new port number.
```
 Port 2224
```
If we want to tighten the security even more we can make so just some IP's are allowed to access SSH.
If you want that just add the following.
```
 AllowUsers *@192.168.234.*
```
In this example every user from a IP that start with 192.168.234. can use it, but if you want to lock it down to only one IP then you should add 192.168.234.1 for a example. You can also add multiple IP's then write it like this.
```
 AllowUsers *@192.168.234.1 *@192.168.234.2
```
Also if you want to lock it to just a user then replace the * before the @ sign with your username.
Now we just need to restart the SSH.
```
 sudo service ssh restart
```

## Activate 2FA for SSH
Now some people will say: Using cert for SSH is more secure etc.
And that's true, but! This is secure enough, becuase I love that I can access SSH to my server from every computer that I want to use and that's something that I can't do if I'm using a cert for SSH.
So if you are following my SSH secure guide and also are following my Fail2Ban guide then you will be secure, that I can promes you.
### Installation and configuration
First we need to install some things, and during the installation you will get security codes, save this codes!
Also scan the  QR-code with your 2FA application in your cellphone so you can generate codes later.
```
 sudo apt-get install libpam-google-authenticator
```
Now you will get a bunch of quastions during the installation.
Just answer Y on everyone except this one, this one you should answer N on.
```
 By default, tokens are good for 30 seconds and in order to compensate for
 possible time-skew between the client and the server, we allow an extra
 token before and after the current time. If you experience problems with poor
 time synchronization, you can increase the window from its default
 size of 1:30min to about 4min. Do you want to do so (y/n) n
```
### Configure SSH to use 2FA
Now we need to configure the SSH to use 2FA.
```
 sudo nano /etc/pam.d/sshd
```
Change the following line so it looks like this, or if you are missing the lines in the document then add them.
```
 @include common-password
 auth required pam_google_authenticator.so
```
Now we need to activate SSH to support this kind of authentication.
```
 sudo nano /etc/ssh/sshd_config
```
Find the line ChallengeResponseAuthentication in this document and change it to Yes.
```
 ChallengeResponseAuthentication yes
```
Now we need to restart the SSH service, then we are done.
```
 sudo service ssh restart
```
Now the next time your connecting to the server with SSH it will ask after your username and password and then it will ask after the 2FA code (token) that you are generating at your cellphone.

# Prevent IP Spoofing
Open the following document and then delete every row in it and replace it with the following rows.
```
 sudo nano /etc/host.conf
```
Replace it with.
```
order bind,hosts
nospoof on
```
After this we need to reboot the server.
```
 sudo reboot
```
# Protect su by limiting access
Run the following commands.
```
sudo groupadd admin
sudo usermod -a -G admin <YOUR ADMIN USERNAME>
sudo dpkg-statoverride --update --add root admin 4750 /bin/su
```
# Harden network with sysctl settings
Now we want to harden the network so let us do that.
We need to change and add some lines to the following document.
```
 sudo nano /etc/sysctl.conf
```
Now we need to add and change this, make sure that you are deleteing the # sign before the rows that already are in sysctl.conf.
```
 # IP Spoofing protection
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Ignore ICMP broadcast requests
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0 
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0

# Ignore send redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# Block SYN attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5

# Log Martians
net.ipv4.conf.all.log_martians = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0 
net.ipv6.conf.default.accept_redirects = 0

# Ignore Directed pings
net.ipv4.icmp_echo_ignore_all = 1
```
Now when we are finsih we just need to restart sysctl.
```
 sudo sysctl -p
```
# Secure shared memory
***If your running Ubuntu on a VM don't use this, it'll break your VM***

This will prevent an attack on the shared memory that softwares are using.
We need to add some lines to the following document.
```
 sudo nano /etc/fstab
```
Then add this.
```
 tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0
```
Now to make this changes to take effect we need to reboot the server.
```
 sudo reboot
```
# Fail2Ban
Fail2Ban are searching trough your logfiles and are blocking IP's that are trying to hack your server in differnet ways.
First we need to install Fail2Ban.
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

### Configuration
As we want our Fail2Ban file not to get deleted if we update Fail2Ban we need to create our own.
```
 sudo nano /etc/fail2ban/jail.local
```
This is the file that you will use to administrate Fail2Ban.
Here is a example file that are good to use, in this config we are protecting SSH against Bruteforce and DDOS attacks.
Everything that are written in Default will be adapted to the rest of the settings in the file so you don't need to write everything over and over again.
```
 [DEFAULT]

 ignoreip = 127.0.0.1/8 # This IP's cant get banned, you can write multi IP's here just use blankspace between them.
 bantime = 172800 # How long a attackers IP will get banned this is 48h.
 findtime = 600 
 maxretry = 0 # How many times a IP are allowed to do the attack before they get banned.
 destemail = YOURMAIL # Make sure that you have ssmtp setup so fail2ban can send you a mail, Read NohaTech-Ubuntu-Ssmtp-English.md
 sendername = Fail2Ban # Make sure that you have ssmtp setup so fail2ban can send you a mail, Read NohaTech-Ubuntu-Ssmtp-English.md
 action = %(action_mwl)s # What should happen this is the setting for that the attacker will get banned and also that you will get a mail ########################## with complete information about it.
 
 [sshd]

 enabled  = true # That the filter is enabled if you want to turn it off then change it to disabled.
 port     = 2324 # What port the filter should be adapted on.
 filter   = sshd # The filter htat we are using.
 logpath  = /var/log/auth.log # Path to the logfile that Fail2Ban will scan.
 maxretry = 3 # I have change this to 3 times as default is 0 times, it happens that you will write the wrong password and it sucks to get       ################banned.

 [sshd-ddos]

 enabled  = true
 port     = 2324
 filter   = sshd-ddos
 logpath  = /var/log/auth.log

```
### End Notes about Fail2Ban
Fail2Ban have so much different filters, you can also add your own filters, to se the settings for Fail2Ban on NGINX or Seafile please read this file NohaTech-Seafile-Guide-English.md I have written about that in there and I'll not write it again in this file.
# UFW
A Firewall is something that everyone wants and needs these days, so I'm going to guide you trough it. As default Ubuntu should have UFW installed but if not, then installed it trough this command.
```
 sudo apt-get install ufw
```
Now we need to open every port that we want to use, it should be atleast the port for SSH and also every other port your using for incoming traffic, you can open the ports like this.
```
 sudo ufw allow 22/tcp
```
In this example we are opening port 22 over TCP, but you can also change tcp for udp and then limit the port to only udp. And if you want to open it both for tcp and udp just write the port number and delete the "/tcp".

Also it's possible to show every port that you have opend then write this command.
```
 sudo ufw status numbered
```
It's recommended to close all of the incoming ports beside that ones that your using and open all of the ports for outgoing traffic as we can't get hacked troug the outgoing traffic. To do that write this commands.
```
 sudo ufw default deny incoming
 sudo ufw default allow outgoing
```
To enable the UFW firewall write this command.
```
 sudo ufw enable
```
And if you want to disable it then replace enable with disable.

You can also do much more with the UFW firewall, you can ban IP's etc. but that's something that I'm not going to write about now, if you just doing it like I have been written it above you should be fine.
# Logwatch
