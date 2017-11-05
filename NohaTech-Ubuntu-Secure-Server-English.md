# Introduction
Well we need to make sure that our server are secure, and here I'll give you some basic things to do to secure your server.

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
