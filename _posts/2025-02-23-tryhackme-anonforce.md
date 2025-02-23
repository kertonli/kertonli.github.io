---
title: 'TryHackMe: Anonforce'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil ]
image:
  path: assets/images/tryhackme/basicpentesting/room.png
---

# ALVO

10.10.79.128

# NMAP

```

┌──(root㉿kali)-[/home/kali]
└─# nmap -F 10.10.79.128                                
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-23 06:50 EST
Nmap scan report for 10.10.79.128
Host is up (0.26s latency).
Not shown: 72 closed tcp ports (reset), 26 filtered tcp ports (no-response)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 2.58 seconds
                                                                             
┌──(root㉿kali)-[/home/kali]
└─# nmap 10.10.79.128 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-23 06:53 EST
Nmap scan report for 10.10.79.128
Host is up (0.22s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 7.75 seconds


```

# FTP

```

ftp 10.10.79.128
Connected to 10.10.79.128.
220 (vsFTPd 3.0.3)
Name (10.10.79.128:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||35674|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
drwxr-xr-x   17 0        0            3700 Feb 23 03:45 dev
drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
drwx------    2 0        0           16384 Aug 11  2019 lost+found
drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread
drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
dr-xr-xr-x   93 0        0               0 Feb 23 03:45 proc
drwx------    3 0        0            4096 Aug 11  2019 root
drwxr-xr-x   18 0        0             540 Feb 23 03:45 run
drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
dr-xr-xr-x   13 0        0               0 Feb 23 03:45 sys
drwxrwxrwt    9 0        0            4096 Feb 23 03:45 tmp
drwxr-xr-x   10 0        0            4096 Aug 11  2019 usr
drwxr-xr-x   11 0        0            4096 Aug 11  2019 var
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz -> boot/vmlinuz-4.4.0-157-generic
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz.old -> boot/vmlinuz-4.4.0-142-generic
226 Directory send OK.
ftp> cd notread
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||52738|)
150 Here comes the directory listing.
-rwxrwxrwx    1 1000     1000          524 Aug 11  2019 backup.pgp
-rwxrwxrwx    1 1000     1000         3762 Aug 11  2019 private.asc
226 Directory send OK.
ftp> get backup.pgp
local: backup.pgp remote: backup.pgp
229 Entering Extended Passive Mode (|||34948|)
150 Opening BINARY mode data connection for backup.pgp (524 bytes).
100% |********************************|   524      777.68 KiB/s    00:00 ETA
226 Transfer complete.
524 bytes received in 00:00 (1.72 KiB/s)
ftp> get private.asc
local: private.asc remote: private.asc
229 Entering Extended Passive Mode (|||51403|)
150 Opening BINARY mode data connection for private.asc (3762 bytes).
100% |********************************|  3762        6.29 MiB/s    00:00 ETA
226 Transfer complete.
3762 bytes received in 00:00 (11.97 KiB/s)
ftp> 
ftp> exit
221 Goodbye.


```

Pegar também o arquivo user.txt dentro de home/melodias (ele possui a secret)

# GPG

Para resetar tudo do gpg: 
cp -r ~/.gnupg ~/.gnupg_backup
rm -rf ~/.gnupg

```

                       ┌───────────────────────────────────────────────────────────────┐
                       │ Please enter the passphrase to import the OpenPGP secret key: │
                       │ "anonforce <melodias@anonforce.nsa>"                          │
                       │ 2048-bit DSA key, ID B92CD1F280AD82C2,                        │
                       │ created 2019-08-12.                                           │
                       │                                                               │
                       │                                                               │
                       │ Passphrase: ********************************_________________ │
                       │                                                               │
                       │         <OK>                                   <Cancel>       │
                       └───────────────────────────────────────────────────────────────┘


Só colocar a secret do arquivo user.txt

```