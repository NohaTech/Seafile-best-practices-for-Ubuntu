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
Now we need to change the port for SSH, as default SSH uses port 22 and everyone knows that so what we want to do is to change it to some other free port for example 2224 or similer.
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
