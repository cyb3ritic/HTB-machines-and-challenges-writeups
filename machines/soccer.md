# Hack The Box - Machine Report: Soccer

![soccer info card](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/soccer/soccer_info.png)

**Author:** Samip Shah  
**Date:** March 11, 2025  
**Machine Name:** Soccer  
**IP Address:** 10.10.11.194  
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

This report documents the step-by-step penetration testing process for the HTB machine **Soccer**. It follows a standard methodology to simulate a real-world penetration test, suitable for inclusion in OSCP-style documentation.

---

## Reconnaissance

### Network Scanning

Performed an initial TCP scan using Nmap to identify open ports and services.

```bash
nmap -sC -sV -oN nmap-inject.txt 10.10.11.100
```

**Results:**
```
PORT     STATE SERVICE         VERSION
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ad:0d:84:a3:fd:cc:98:a4:78:fe:f9:49:15:da:e1:6d (RSA)
|   256 df:d6:a3:9f:68:26:9d:fc:7c:6a:0c:29:e9:61:f0:0c (ECDSA)
|_  256 57:97:56:5d:ef:79:3c:2f:cb:db:35:ff:f1:7c:61:5c (ED25519)
80/tcp   open  http            nginx 1.18.0 (Ubuntu)
|_http-title: Soccer - Index 
|_http-server-header: nginx/1.18.0 (Ubuntu)
9091/tcp open  xmltec-xmlmail?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, RPCCheck, SSLSessionReq, drda, informix: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
```

### Web Enumeration

The HTTP service on port 80 hosted a website that redirects to `soccer.htb`. Add The domain in /etc/host file. The website must be accessible now.


Conducted directory enumeration using Gobuster:

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ gobuster dir -u http://soccer.htb -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt -t 50
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://soccer.htb
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/tiny                 (Status: 301) [Size: 178] [--> http://soccer.htb/tiny/]
Progress: 62281 / 62282 (100.00%)
===============================================================
Finished
===============================================================

```

**Discovered Directories:**
```
/tiny
```

Inspected /tiny page manually and it revealed a H3K Tiny File Manager login form.
![tiny login page](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/soccer/tiny_login_page.png)

---