
# Introduction
If you want multiple servers over the HTTP and HTTPS port and you only have one (WAN)(INTERNET) IP you need a reverse proxy that can route the traffic to the correct server. The good thing here is that you can have everything on the reverse proxy, the protection and also the SSL certs etc. You can host this server on it's own machine or simpley on a VM.

Before you start to setup the reverse proxy I do recommend that you are following the guides that are listed below:
* How to secure your Ubuntu server <br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Secure-Server.md
* How to setup Hyper-V in the right way<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Run-Hyper-V.md
* How to install and setup SSMTP<br>
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/Ubuntu-Guides/Install-ssmtp.md

We will use a text editor called nano in this guide, so here is some commands that you need to know.
```
ctrl+k = deletes a hole row in the document

ctrl+w = you can search in the document.
 
ctrl+x = your asked if you want to save the document or not, answer y or n then press enter and then after that you will get back to the     terminal.
```
