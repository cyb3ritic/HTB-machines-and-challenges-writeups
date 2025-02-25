# <center> Cozyhosting </center>
<p align="center"> 
    <img src="https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/cozyhosting_info.png">
</p>

Hello Hackerrrrrs. How y all doin' ? Welcome to my new walkthrough in a new linux based easy labeled htb box `cozyhosting`.

Today in this section, we will .....


let's start with our box. As usual I have connected to my htb vpn server.
- Machine's IP: `10.10.11.230`
- My IP: `10.10.14.3`

## Phase 1: Scanning

- We will begin with our normal nmap scan to see what's been hidded behind this machine's server using the following command: `nmap -A -T4 10.10.14.3 -Pn`
    - -A : agressive scan (gives us services and their version information)
    - -T4: For fast speed. (ranges from T0 to T5, T5 being the fastest)
    - -Pn: to treat all the host as online

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ nmap -A -T4 10.10.11.230 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-26 01:10 IST
Nmap scan report for 10.10.11.230
Host is up (0.086s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 43:56:bc:a7:f2:ec:46:dd:c1:0f:83:30:4c:2c:aa:a8 (ECDSA)
|_  256 6f:7a:6c:3f:a6:8d:e2:75:95:d4:7b:71:ac:4f:7e:42 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://cozyhosting.htb
|_http-server-header: nginx/1.18.0 (Ubuntu)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 143/tcp)
HOP RTT      ADDRESS
1   94.10 ms 10.10.14.1
2   94.12 ms 10.10.11.230
```
Hmm, So 2 ports open, 22(for SSH) and 80(for http) and http is trying to redirect to `http://cozyhosting.htb`. So let's add this domain to our `/etc/hosts` file using `sudo nano /etc/hosts` command.

![adding domain to /etc/hosts file](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/domain.png)

Now we are good to go. Let's explore the website running on port 80 `http://cozyhosting.htb`. Here we get a static web page with no functional links. And a login page. 

![landing page of website](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/homepage.png)


Tried fuzzing the login form with some default credentials but couldnot break in. But the interesting fact is we were having a cookie named `JSESSIONID` stored.

![login page of website](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/loginpage.png)


## Phase 2: Enumeration

Let's try digging into the webpage for some hidden contents. We will try directory bruteforcing, and subdomain/vhost enumeration in this step.

1. Directory Bruteforcing `gobuster dir -u http://cozyhosting.htb -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt -t 50`
    - dir: for directory mode
    - -u: to specify url 
    - -w: to specify wordlist
    - -t: number of threads
```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ gobuster dir -u http://cozyhosting.htb -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt -t 50
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://cozyhosting.htb
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/login                (Status: 200) [Size: 4431]
/admin                (Status: 401) [Size: 97]
/logout               (Status: 204) [Size: 0]
/error                (Status: 500) [Size: 73]
/index                (Status: 200) [Size: 12706]
/plain]               (Status: 400) [Size: 435]
/[                    (Status: 400) [Size: 435]
/]                    (Status: 400) [Size: 435]
/quote]               (Status: 400) [Size: 435]
/extension]           (Status: 400) [Size: 435]
/[0-9]                (Status: 400) [Size: 435]
/[0-1][0-9]           (Status: 400) [Size: 435]
/20[0-9][0-9]         (Status: 400) [Size: 435]
/[2]                  (Status: 400) [Size: 435]
/index                (Status: 200) [Size: 12706]
/[2-9]                (Status: 400) [Size: 435]
/options[]            (Status: 400) [Size: 435]
Progress: 62284 / 62285 (100.00%)
===============================================================
Finished
===============================================================
```
- /login : a login page that we have seen earlier
- /admin : redirects back to login page 
- /error : Interesting one. It gives a whitelabel error page.
    - [white label error page](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/errorpage.png)

    Here we are just one google search away from knowing that it is the default error page of Java's spring boot framework. Hmmm.... So the website is using spring boot. Cool. 

    - [Google search for white label error page](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/springboot_errorpage.png)


Hey, Do you know Seclists provide a wordlist particularly made for websites using springboot framework? Let's use this wordlist and enumerate the directories again.

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ gobuster dir -u http://cozyhosting.htb -w /usr/share/wordlists/seclists/Discovery/Web-Content/spring-boot.txt -t 50           
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://cozyhosting.htb
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/spring-boot.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/actuator/env         (Status: 200) [Size: 4957]
/actuator/env/lang    (Status: 200) [Size: 487]
/actuator             (Status: 200) [Size: 634]
/actuator/env/home    (Status: 200) [Size: 487]
/actuator/env/path    (Status: 200) [Size: 487]
/actuator/health      (Status: 200) [Size: 15]
/actuator/mappings    (Status: 200) [Size: 9938]
/actuator/sessions    (Status: 200) [Size: 48]
Progress: 112 / 113 (99.12%)
/actuator/beans       (Status: 200) [Size: 127224]
===============================================================
Finished
===============================================================
```

- There you go. The actuator end point is accessible. For all of you who do not know about `actuator`, Spring Boot Actuator is a module that helps you monitor and manage your Spring Boot application. It provides production-ready features like dependency management, auto-configuration, and health indicators


## Phase 3: Gaining initial foothold

`http://cozyhosting/actuator/sessions` leaked a session id of a user `kanderson` who i guessed might be logged in. I edited my JSESSIONID cookie to that of `kanderson`
![session hijacking of kanderson](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/session_hijacking.png)

After this cookie forgery, going back to the landing page show me that i am logged in as `kanderson`. So, we just hijacked the session and we are logged in. Let's try accessing /admin page now.  And voila, we were able to access the admin dashboard.

![admin dashboard](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/admin_dashboard.png)