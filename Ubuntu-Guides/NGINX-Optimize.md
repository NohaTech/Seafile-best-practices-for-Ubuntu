# Introduction
As I have done many guides, I need to spare some time. So I have made this document seperated so I can just link to it from the guides to save time.

## Configuration
So now we want to optimize NGINX for best performence.
First we are going to look how many working processors we have.
```
grep processor /proc/cpuinfo | wc -l
```
In my case I did get 8.
And now we need to see how many worker connections we can have simultaneously.
```
ulimit -n
```
In my case I did get 1024.
Now we need to add this new numbers to our NGINX configuration file.
```
sudo nano /etc/nginx/nginx.conf
```
Then we need to change this two rows, and add your number in the end of the lines.
```
worker_processes 8;
worker_connections 1024;
```
Now we need to restart NGINX so this changes can take effect.
```
sudo service nginx restart
sudo service nginx reload
```
Now we are done.
