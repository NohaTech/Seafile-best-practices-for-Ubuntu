# NGINX Self signed cert (HTTPS)
I have saved this document as someone maybe want to use self signed cert for some reason, but I recommend Let's Encrypts free cert instead.
You can find out in the Seafile main guide how to get the Let's Encrypt cert to work with Seafile.
***Self sign cert don't work with the Windows client, you need a good cert for that client to work***

## Configuration

First we need to create privkey.pem and cacert.pem.
```
 sudo openssl genrsa -out /etc/ssl/private/privkey.pem 2048
 sudo openssl req -new -x509 -key /etc/ssl/private/privkey.pem -out /etc/ssl/private/cacert.pem -days 1095
```
If you get any issues when your trying to create the cert's do it this way insted. Replace xxx with your user name. (optional)
```
 cd ~
 sudo openssl genrsa -out /home/xxx/privkey.pem 2048
 sudo openssl req -new -x509 -key /home/xxx/privkey.pem -out /home/xxx/cacert.pem -days 1095
 sudo mv privkey.pem /etc/ssl/private/
 sudo mv cacert.pem /etc/ssl/private/
```
Then we need to create the dhparam.pem file, it's a little tricky we need to create it in our home folder then move it to the /etc/ssl/private/ folder.
```
 cd ~
 sudo openssl dhparam 2048 > dhparam.pem
 sudo mv dhparam.pem /etc/ssl/private/dhparam.pem
```
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
        listen       80;
        server_name  seafile.example.com;
        rewrite ^ https://$http_host$request_uri? permanent;    # force redirect http to https
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin" always;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
        server_tokens off;
    }
    server {
        listen 443;
        ssl on;
        ssl_certificate /etc/ssl/private/cacert.pem;        # path to your cacert.pem
        ssl_certificate_key /etc/ssl/private/privkey.pem;    # path to your privkey.pem
        server_name seafile.example.com;
        ssl_session_timeout 5m;
        ssl_session_cache shared:SSL:5m;

        # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
        ssl_dhparam /etc/ssl/private/dhparam.pem;

        # secure settings (A+ at SSL Labs ssltest at time of writing)
        # see https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-SEED-SHA:DHE-RSA-CAMELLIA128-SHA:HIGH:!aNULL:!eNULL:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS';
        ssl_prefer_server_ciphers on;

        proxy_set_header X-Forwarded-For $remote_addr;

        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Frame-Options "DENY" always;
        add_header Referrer-Policy "strict-origin" always;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
        server_tokens off;

        location / {
            proxy_pass         http://127.0.0.1:8000;
            proxy_set_header   Host $host;
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Host $server_name;
            proxy_set_header   X-Forwarded-Proto https;
            proxy_request_buffering off;

            access_log      /var/log/nginx/seahub.access.log;
            error_log       /var/log/nginx/seahub.error.log;

            proxy_read_timeout  1200s;

            client_max_body_size 0;
        }

        location /seafhttp {
            rewrite ^/seafhttp(.*)$ $1 break;
            proxy_pass http://127.0.0.1:8082;
            client_max_body_size 0;
            proxy_connect_timeout  36000s;
            proxy_request_buffering off;
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
