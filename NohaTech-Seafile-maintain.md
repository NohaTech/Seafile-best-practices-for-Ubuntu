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
