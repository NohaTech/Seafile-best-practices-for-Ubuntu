# Introduction
As I have written many guides, I need to save some time. So I have made this document seperated so I can just link to it from the guides to save time and also when I need to change something I can just do it in one document.<br>
What we are going to do here is that we are going to secure NGINX with Fail2Ban filters that are adopted for NGINX.
I also writting the Fail2Ban filter for SSH in this guide as we all want to protect our SSH.

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```
### Add filters
What we need to do first here is to create the filters that we need, some of them are already created as default in Fail2Ban but some we need to create by our self.

Let us go to the filter.d folder before we begin.
```
cd /etc/fail2ban/filter.d/
```

First we are going to add a line in a filter.
```
sudo nano /etc/fail2ban/filter.d/nginx-http-auth.conf
```
And then we need to add this line, add it under the line that almost look the same.
```
^ \[error\] \d+#\d+: \*\d+ no user/password was provided for basic authentication, client: <HOST>, server: \S+, request: "\S+ \S+ HTTP/\d+\.\d+", host: "\S+"\s*$
```
Then we need to copy a filter from Apache2 to NGINX.
```
sudo cp apache-badbots.conf nginx-badbots.conf
```
Then we are going to create our first filter.
```
sudo nano /etc/fail2ban/filter.d/nginx-forbidden.conf
```
Then add this to the file
```
[Definition]

failregex = ^ \[error\] \d+#\d+: .* forbidden .*, client: <HOST>, .*$

ignoreregex =
```
Create
```
sudo nano /etc/fail2ban/filter.d/nginx-nohome.conf
```
Add
```
[Definition]

failregex = ^<HOST> -.*GET .*/~.*

ignoreregex =
```
Create
```
sudo nano /etc/fail2ban/filter.d/nginx-noproxy.conf
```
Add
```
[Definition]

failregex = ^<HOST> -.*GET http.*

ignoreregex =
```
Create
```
sudo nano /etc/fail2ban/filter.d/nginx-noscript.conf
```
Add
```
[Definition]

failregex = ^<HOST> -.*GET.*(\.php|\.asp|\.exe|\.pl|\.cgi|\.scgi)

ignoreregex =
```
