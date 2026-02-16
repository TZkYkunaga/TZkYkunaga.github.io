---
title: SMB Basic
published: 2026-02-16
description: 'smb - Port 445, 139, 138, and 137'
image: 'guide/smbbackground.jpg'
tags: [smb, smbmap,HTB]
category: 'learning'
draft: false 
lang: 'en'
---


`List the SMB shares available on the target host. Connect to the available share as the bob user. Once connected, access the folder called 'flag' and submit the contents of the flag.txt file.`

Link to the LAB  [Hack The Box - Academy : Getting Started !!!](https://academy.hackthebox.com/module/77/section/726)

Target IP : 10.129.42.254

```python
┌──(kali㉿kali)-[~/Downloads/Wordlist]
└─$ sudo nmap 10.129.42.254 -sV -sC -T 5
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-16 01:53 -0500
Nmap scan report for 10.129.42.254
Host is up (0.28s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.244
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Feb 25  2021 pub
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:01:d7:79:e9:d2:09:2a:b8:d9:b4:9a:6c:00:0c:1c (RSA)
|   256 2b:99:b2:1f:ec:1a:5a:c6:b7:be:b5:50:d1:0e:a9:df (ECDSA)
|_  256 e4:f8:17:8d:d4:71:d1:4e:d4:0e:bd:f0:29:4f:6d:14 (ED25519)
80/tcp   open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: PHP 7.4.3 - phpinfo()
139/tcp  open  netbios-ssn Samba smbd 4
445/tcp  open  netbios-ssn Samba smbd 4
2323/tcp open  telnet      Linux telnetd
8080/tcp open  http        Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Apache Tomcat
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: GS-SVCSCAN, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time: 
|   date: 2026-02-16T06:53:39
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.29 seconds

```

There is a tool help us mapping SMB

```python
smbmap -H 10.129.42.254
```

```python
┌──(kali㉿kali)-[~/Downloads/Wordlist]
└─$ smbmap -H 10.129.42.254   

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 0 authenticated session(s)                                                          
                                                                                                                             
[+] IP: 10.129.42.254:445       Name: 10.129.42.254             Status: NULL Session
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        users                                                   NO ACCESS
        IPC$                                                    NO ACCESS       IPC Service (gs-svcscan server (Samba, Ubuntu))

```

```python
using credentials for the user bob (bob:Welcome1).
```

```python
smbclient \\\\10.129.42.254\\users
```

```python
┌──(kali㉿kali)-[~/Downloads/Wordlist]
└─$ smbclient \\\\10.129.42.254\\users -U 'bob'
Password for [WORKGROUP\bob]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Feb 25 18:06:52 2021
  ..                                  D        0  Thu Feb 25 15:05:31 2021
  flag                                D        0  Thu Feb 25 18:09:26 2021
  bob                                 D        0  Thu Feb 25 16:42:23 2021

                4062912 blocks of size 1024. 1276004 blocks available
smb: \> cd flag
smb: \flag\> ls
  .                                   D        0  Thu Feb 25 18:09:26 2021
  ..                                  D        0  Thu Feb 25 18:06:52 2021
  flag.txt                            N       33  Thu Feb 25 18:09:26 2021
smb: \flag\> more flag.txt
getting file \flag\flag.txt of size 33 as /tmp/smbmore.ckTBzz (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)

dceece590f3284c3866305eb2473d099
/tmp/smbmore.ckTBzz (END)

#click q to exit
```

Flag : `dceece590f3284c3866305eb2473d099`
