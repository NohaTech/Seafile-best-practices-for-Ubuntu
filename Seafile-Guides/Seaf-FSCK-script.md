# Introduction
It's good to have some kind of knowledge regarding the health of your data.
So what I have done is that I have added a little script in crontab monthly folder, that means that once a month it will run.
And if you have installed and setup ssmtp then you will get a e-mail from the server with the results, and also you will have a logfile named seaf-fsck.log under the logs folder that are located at your Seafile folder in my case it's located /opt/nohatech/logs.
You can read in my other guide how to setup ssmtp. ( https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md )

Remember to change every path to the right one for you.

## Setup
First we need to go to the right folder.
```
cd /etc/cron.monthly
```
Then we need to create the script file.
```
sudo nano seaf-fsck-checker.sh 
```
Then we need to add this following lines to it.
```
#!/bin/bash
# stop the server
echo Starting the seaf-fsck script...
sudo -u seafile /opt/nohatech/seafile-server-latest/seaf-fsck.sh >> /opt/nohatech/logs/seaf-fsck.log
sleep 5
echo Everything is done!
```
Make sure that the script has been given execution rights, to do that run this command.
```
sudo chmod +x /etc/cron.monthly/seaf-fsck-checker.sh
```
Now we are done and we will get a report to our mail and also to the seaf-fsck.log file once a month.
