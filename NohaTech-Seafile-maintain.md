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
## Check librarys after corruptions.
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
https://manual.seafile.com/maintain/seafile_fsck.html
