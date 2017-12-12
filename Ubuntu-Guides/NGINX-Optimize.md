# Introduction
As I have written many guides, I need to save some time. So I have made this document seperated so I can just link to it from the guides to save time and also when I need to change something I can just do it in one document.

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```

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
