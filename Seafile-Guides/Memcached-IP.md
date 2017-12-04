# Introduction
This is not best practices but if you for some reason want to use this you should know that Memcached over socket is 30% faster then over IP. <br>
You can find how to setup memcached with socket in the "main guide", if you have been following the "main guide" you have also already installed the necessary package for Memecahced, but if you have not done that take a look in the "main guide" and install all of the package listed there.

## Setup
***If your running 6.2.3 you need to do a fix before using memcached*** <br> <br>
Before your doing the changes you need to stop Seafile.
```
cd /opt/nohatech/seafile-server-latest/
./seafile.sh stop
./seahub.sh stop
```
```
nano /opt/nohatech/seafile-server-latest/runtime/seahub.conf
```
And then you need to add this line.
```
threads = 5
```
***End of the bugfix***

First we need to stop Seafile and memcached service.
```
cd /opt/nohatech/seafile-server-latest/
./seafile.sh stop
./seahub.sh stop
sudo service memcached stop
```
Then we need to make sure that we have the memecached config file setup right.
```
sudo nano /etc/memcached.conf
```
Replace all of the text to in the file to this.
```
# part of the Debian GNU/Linux distribution.

# Run memcached as a daemon. This command is implied, and is not needed for the
# daemon to run. See the README.Debian that comes with this package for more
# information.
-d

# Log memcached's output to /var/log/memcached
logfile /var/log/memcached.log

# Be verbose
#-v
 
# Be even more verbose (print client commands as well)
#-vv
 
# Start with a cap of 64 megs of memory. It's reasonable, and the daemon default
# Note that the daemon will grow to this size, but does not start out holding this much
# memory
-m 64

# Default connection port is 11211
-p 11211

# Run the daemon as root. The start-memcached will default to running as root if no
# -u command is present in this config file
-u memcache

# Specify which IP address to listen on. The default is to listen on all IP addresses
# This parameter is one of the only security measures that memcached has, so make sure
# it's listening on a firewalled interface.
-l 127.0.0.1

# Limit the number of simultaneous incoming connections. The daemon default is 1024
# -c 1024

# Lock down all paged memory. Consult with the README and homepage before you do this
# -k

# Return error when memory is exhausted (rather than removing items)
-M

# Maximize core file limit
# -r
```
It's hard to tell for me how much memory you should use for Memcached, you can change it in the "-m 64" line and replace 64 with the amount of memory you want it's written in MB. I personly uses 1024 and that works for me.

Now we need to change the seahub_settings.py file.
```
nano /opt/nohatech/conf/seahub_settings.py
```
Then add the following lines in the bottom of the file.
```
# Memcached Settings
CACHES = {
   'default': {
       'BACKEND': 'django_pylibmc.memcached.PyLibMCCache',
       'LOCATION': '127.0.0.1:11211',
   }
}
```
Now as we have changed to use Memcached we need to force-reload memcache and start Seafile again.
```
sudo service memcached force-reload
cd /opt/nohatech/seafile-server-latest/
./seafile.sh start
./seahub.sh start
```
