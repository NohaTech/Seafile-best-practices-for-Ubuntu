# Introduction
It's recommended to use SSL cert, you can use self signed or a free one from Let's Encrypt, you can read how to configure it in the "main guide" but if you still for some reason want to use HTTP only without SSL cert then follow this guide.
I do assume that you have skipped the NGINX installation part from the "main guide".

## Setup
First we need to delete the old / default configurations.
Just so your aware of it, NGINX are sensetiv how you are writing the configuration file with spaces etc.
```
sudo rm -rf /etc/nginx/sites-enabled/default
sudo rm -rf /etc/nginx/sites-available/default
```
Then we need to create the file that we are going to use.
```
sudo nano /etc/nginx/sites-available/seafile.conf
```
And copy this in to the file, and remember to replace the seafile.example.com to your own domain and also change the path for the location folder in the bottom the yours.
```
server {
    listen 80;
    server_name seafile.example.com;

    proxy_set_header X-Forwarded-For $remote_addr;

    location / {
         proxy_pass         http://127.0.0.1:8000;
         proxy_set_header   Host $host;
         proxy_set_header   X-Real-IP $remote_addr;
         proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header   X-Forwarded-Host $server_name;
         proxy_read_timeout  1200s;
         proxy_request_buffering off;
         
         add_header X-Content-Type-Options "nosniff" always;
         add_header X-XSS-Protection "1; mode=block" always;
         add_header X-Frame-Options "DENY" always;
         add_header Referrer-Policy "strict-origin" always;
         add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
         server_tokens off;

         # used for view/edit office file via Office Online Server
         client_max_body_size 0;

         access_log      /var/log/nginx/seahub.access.log;
         error_log       /var/log/nginx/seahub.error.log;
    }
    
    location /seafhttp {
        rewrite ^/seafhttp(.*)$ $1 break;
        proxy_pass http://127.0.0.1:8082;
        client_max_body_size 0;

        proxy_connect_timeout  36000s;
        proxy_read_timeout  36000s;
        proxy_send_timeout  36000s;

        send_timeout  36000s;
    }
    location /media {
        root /opt/nohatech/seafile-server-latest/seahub;
    }
}
```
Now we are going to activate our configuration file.
```
sudo ln -s /etc/nginx/sites-available/seafile.conf /etc/nginx/sites-enabled/seafile.conf
```
Now we are almoste done, we just need to restart NGINX.
```
sudo service nginx reload
sudo service nginx restart
```
