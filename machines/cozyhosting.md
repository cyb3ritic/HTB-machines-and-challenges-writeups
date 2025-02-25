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

Now we are good to go. Let's explore the website running on port 80 `http://cozyhosting.htb`. Here we get a static web page with no functional links. And a login page. Tried fuzzing the login form with some default credentials but couldnot break in. 

![landing page of website](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/homepage.png)

![login page of website](https://raw.githubusercontent.com/cyb3ritic/images/refs/heads/master/htb/machines/cozyhosting/loginpage.png)