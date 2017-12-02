# *** THIS DOCUMENT ARE UNDER CONSTRUCTION ***
# Introduction
Now when you have a Seafile server up and running, you also want to maintain it and you also might want to check that everything works from time to time.

## Check the DB's.
Well, this is something regarding MariaDB but as our databases are in MariaDB we need to check so everything is OK from time to time.
First we need to shutdown Seafile.
```
 cd /opt/nohatech/seafile-server-latest/
 ./seafile.sh stop
 ./seahub.sh stop
```
Now when can run the command to check the DB's.
```
 sudo mysqlcheck -c  -u root -p --all-databases
```
You should see a list and at the right it should say "OK" on every row.
So everything is OK, let us start Seafile again.
```
 ./seafile.sh start
 ./seahub.sh start
```
## Check libraries after corruptions
It happens, sometimes a librariy get's corrupted or a file.
So what should we do about it? Well, Seafile have a good tool called seaf-fsck.sh.
You can run it at the same time as your Seafile server are running, no need to shut the server down.
This can take a long time to run depending on how much data your storing.
To run the check.
```
 cd /opt/nohatech/seafile-server-latest/
 ./seaf-fsck.sh
```
If you need to do some repairs or some other issues, take a look at the Seafile Manual how to solve them.
Remember to shutdown your Seafile server before you start repairs or something else. It's only the check function that you can run at the same time that the server are up and running.
https://manual.seafile.com/maintain/seafile_fsck.html

And as it can take a long time to run the seaf-fsck.sh check you can choose to write the output to a .log file instead. If you are using this command you can close the SSH window and the server will still run the seaf-fsck.sh and log it to the file.
But remember to change the "nohatech" in the path to your own, in this example we are logging to a file in the "logs" that belongs to Seafile.
```
 /opt/nohatech/seafile-server-latest/seaf-fsck.sh >> /opt/nohatech/logs/seaf-fsck.log &
```

## Logfiles
Sometimes you want to take a look at the logfiles to see that everything looks OK.
The Seafile logfiles are located here.
```
 cd /opt/nohatech/logs/
```
