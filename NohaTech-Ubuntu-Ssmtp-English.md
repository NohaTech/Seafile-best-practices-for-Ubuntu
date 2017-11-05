# Introduction
It's nice to get mails from your server when something is wrong or you just maybe want a application to send you a mail with a logfile or something.
But it's not so nice when you need a hole mail server, but we can use ssmtp and that's a application that allows you to only send mails from your server but it will send it trough some other mail service.
So it's like Outlook or Thunderbird but for servers.
In this guide we are going to use Gmail to send mail from.

We will use a texteditor called nano in this guide, so here is some commands that you need to know.
```
 ctrl+k = deletes a hole row in the document
 
 ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```

## Installation and setup
First we need to install it.
```
 sudo apt-get install ssmtp
```
Then we need to make some configurations.
```
 sudo nano /etc/ssmtp/ssmtp.conf
```
Then add / change it to this.
```
 # Config file for sSMTP sendmail
 #
 # The person who gets all mail for userids < 1000
 # Make this empty to disable rewriting.
 root=YOURMAIL #The mail that you want the server to send all the mails to.

 # The place where the mail goes. The actual machine name is required no
 # MX records are consulted. Commonly mailhosts are named mail.domain.com
 mailhub=smtp.gmail.com:587

 AuthUser=YOURGMAILADRESS #For example (nohatech@gmail.com)
 AuthPass=YOURGMAILPASSWORD
 UseTLS=YES
 UseSTARTTLS=YES

 # Where will the mail seem to come from?
 rewriteDomain=gmail.com

 # The full hostname
 hostname=YOURHOSTNAME #Example test.nohatech.se

 # Are users allowed to set their own From: address?
 # YES - Allow the user to specify their own From: address
 # NO - Use the system generated From: address
 FromLineOverride=YES

```
Now we just need to reboot the server then everything is done!
```
 sudo reboot
```
