# <center>Lame</center>
<p align='center'><img src="../.medias/lame/lame_logo.png"></p>

Okay friends, today we will solve an easy labeled linux based HTB machine `Lame`.

## Initial step

- connect to htb vpn and spawn the machine.
  
## Scanning and Enumeration

### nmap scan
- `nmap -sC -sV 10.10.10.3 -Pn`
```bash
┌──(cyb3ritic㉿kali)-[~]
└─$ nmap -sC -sV 10.10.10.3 -Pn 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-06 21:00 IST
Nmap scan report for 10.10.10.3
Host is up (0.23s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.7
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 2h00m23s, deviation: 2h49m45s, median: 21s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2024-08-06T11:31:30-04:00
|_smb2-time: Protocol negotiation failed (SMB2)

```

**Important things to notice:**
- ftp is running on port 21. 
    - Anonymous login is allowed.
    - vsFTPd 2.3.4
- ssh on port 22 (as usual)
- samba on 139 and 445

