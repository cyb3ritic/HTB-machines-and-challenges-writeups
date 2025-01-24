# <center>Nibbles</center>

<p align='center'><img src="https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/nibbles/nibbles_info.png"></p>

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

- Using some guesses and a fact that in older HTB machines, generally teh name of machine is the password, I was able to login in admin.php with credentials `admin:nibbles`.


# the machine is broken on HTB. so i'll be continuing it after somedays. take a break.

