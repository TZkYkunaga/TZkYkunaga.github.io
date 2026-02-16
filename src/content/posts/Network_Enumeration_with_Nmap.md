---
title: Network Enumeration with Nmap - Firewall and IDS/IPS Evasion
published: 2026-02-16
description: 'This my my write-up'
image: 'guide/nebackground.png'
tags: [nmap, scanning, learning]
category: 'learning'
draft: false 
lang: 'en'
---

Hi! :)
## E4sy

 `Our client wants to know if we can identify which operating system their provided machine is running on. Submit the OS name as the answer.`

At first try, i used `-O`  to detect os but Linux is `wrong`

```python
sudo nmap -T 5 --max-retries 2 --initial-rtt-timeout 50ms  --max-rtt-timeout 100ms 10.129.7.114 -O -Pn -n --disable-arp-ping -sn
```

```python
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-15 14:11 UTC
Warning: 10.129.7.114 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.7.114
Host is up (0.21s latency).
Not shown: 810 closed tcp ports (reset), 187 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
10001/tcp open  scp-config
Aggressive OS guesses: Linux 4.15 - 5.8 (95%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.95 seconds
```

Next try i use -sC which is script :) and it show that `Apache2 Ubuntu Default Page` in port 80 http 

```python
sudo nmap -T 5 --max-retries 1 --initial-rtt-timeout 50ms  --max-rtt-timeout 100ms 10.129.7.114  -Pn -sC
```

```python
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-15 14:30 UTC
Warning: 10.129.7.114 giving up on port because retransmission cap hit (1).
Nmap scan report for 10.129.7.114 (10.129.7.114)
Host is up (0.27s latency).
Not shown: 546 closed tcp ports (reset), 451 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
80/tcp    open  http
|_http-title: Apache2 Ubuntu Default Page: It works
10001/tcp open  scp-config

Nmap done: 1 IP address (1 host up) scanned in 31.55 seconds
```

So the answer is 

> `Ubuntu`
> 

## Medium

`After the configurations are transferred to the system, our client wants to know if it is possible to find out our target's DNS server version. Submit the DNS server version of the target as the answer.`

First attempt i performed a quick scan 

> sudo nmap -T 5 --max-retries 1 --initial-rtt-timeout 50ms  --max-rtt-timeout 100ms -Pn --disable-arp-ping 10.129.7.122 -p-
> 

```python

Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-15 14:41 UTC
Warning: 10.129.7.122 giving up on port because retransmission cap hit (1).
Nmap scan report for 10.129.7.122 (10.129.7.122)
Host is up (0.34s latency).
Not shown: 55304 closed tcp ports (reset), 10224 filtered tcp ports (no-response)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
53/tcp  open  domain
80/tcp  open  http
110/tcp open  pop3
139/tcp open  netbios-ssn
143/tcp open  imap

Nmap done: 1 IP address (1 host up) scanned in 133.14 seconds
```

> sudo nmap -T 5 --max-retries 1 --initial-rtt-timeout 50ms  --max-rtt-timeout 100ms -Pn --disable-arp-ping 10.129.7.122 -p 53 -sV
> 

My nmap scan was too fast soo it filtere

```python

Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-15 14:48 UTC
Nmap scan report for 10.129.7.122 (10.129.7.122)
Host is up.

PORT   STATE    SERVICE VERSION
53/tcp filtered domain
```

> sudo nmap -Pn --disable-arp-ping 10.129.7.122 -p 53 -sV  -n
> 

```python
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-15 14:49 UTC
Nmap scan report for 10.129.7.122
Host is up (0.27s latency).

PORT   STATE SERVICE VERSION
53/tcp open  domain  (unknown banner: HTB{GoTtgUnyze9Psw4vGjcuMpHRp})
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.94SVN%I=7%D=2/15%Time=6991DD04%P=x86_64-pc-linux-gnu%r(D
SF:NSVersionBindReqTCP,59,"\0W\0\x06\x85\0\0\x01\0\x01\0\x01\0\0\x07versio
SF:n\x04bind\0\0\x10\0\x03\xc0\x0c\0\x10\0\x03\0\0\0\0\0\x1f\x1eHTB{GoTtgU
SF:nyze9Psw4vGjcuMpHRp}\xc0\x0c\0\x02\0\x03\0\0\0\0\0\x02\xc0\x0c");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.68 seconds
```

Flag : `HTB{GoTtgUnyze9Psw4vGjcuMpHRp}`

## HArd

 `Now our client wants to know if it is possible to find out the version of the running services. Identify the version of service our client was talking about and submit the flag as the answer.`

> sudo nmap 10.129.5.196  -Pn  --disable-arp-ping --packet-trace --source-port 53 -sV   -sS -D RND:5 -O
> 

```python
Nmap scan report for 10.129.5.196 (10.129.5.196)
Host is up (0.31s latency).
Not shown: 869 closed tcp ports (reset), 128 filtered tcp ports (no-response)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http       Apache httpd 2.4.29 ((Ubuntu))
50000/tcp open  tcpwrapped
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=2/16%OT=22%CT=1%CU=40016%PV=Y%DS=2%DC=I%G=Y%TM=6992
OS:A4D1%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=C)
OS:SEQ(SP=104%GCD=1%ISR=109%TI=Z%CI=Z%TS=C)SEQ(SP=104%GCD=1%ISR=109%TI=Z%CI
OS:=Z%TS=D)SEQ(SP=104%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M552ST11NW7%
OS:O2=M552ST11NW7%O3=M552NNT11NW7%O4=M552ST11NW7%O5=M552ST11NW7%O6=M552ST11
OS:)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W
OS:=FAF0%O=M552NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N
OS:)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7
OS:(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=
OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

> sudo nmap 10.129.5.196  -p50000 -sS -Pn -n --disable-arp-ping --packet-trace
> 

```python
SENT (0.0325s) TCP 10.10.14.244:46858 > 10.129.5.196:50000 S ttl=37 id=34807 iplen=44  seq=3054002208 win=1024 <mss 1460>
SENT (1.0336s) TCP 10.10.14.244:46860 > 10.129.5.196:50000 S ttl=54 id=34538 iplen=44  seq=3054133282 win=1024 <mss 1460>
Nmap scan report for 10.129.5.196
Host is up.

PORT      STATE    SERVICE
50000/tcp filtered ibm-db2

Nmap done: 1 IP address (1 host up) scanned in 2.07 seconds
```

> ncat -nv --source-port 53 10.129.5.196  50000
> 

```python
Ncat: Version 7.94SVN ( https://nmap.org/ncat )
libnsock mksock_bind_addr(): Bind to 0.0.0.0:53 failed (IOD #1): Permission denied (13)
Ncat: TIMEOUT.
```

> sudo ncat -nv --source-port 53 10.129.5.196  50000
> 

:)) sometimes we need to run it with sudo 

```python
──(kali㉿kali)-[~/Downloads/Wordlist]
└─$ sudo ncat -nv --source-port 53 10.129.5.196  50000
Ncat: Version 7.98 ( https://nmap.org/ncat )
Ncat: Connected to 10.129.5.196:50000.

220 HTB{kjnsdf2n982n1827eh76238s98di1w6}

```

Flag :  HTB{kjnsdf2n982n1827eh76238s98di1w6}