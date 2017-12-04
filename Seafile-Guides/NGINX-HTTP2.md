# Introduction
Here is how you can activate HTTP2, as not all browsers are supporting HTTP2 yet (2017) it's have a bad effect it's making things slower.
But if the browsers are supporting HTTP2 it should be faster. In this guide I do assume that you have already followed the "main guide".<br>
<br>
HTTP2 support are only supported in NGINX version>=1.9.5 and it's only supported with SSL activated.

## How to activate
What we need to do is to add HTTP2 support in the NGINX configuration file.
```
sudo nano /etc/nginx/sites-available/seafile.conf
```
And then you should just add "http2" after every "listen 443" like this.
```
listen 443 http2;
```
