---
title: 'TryHackMe: Rabbit Store'
author: Kertonli
categories: [TryHackMe]
tags: [Médio, Nmap]
image:
  path: assets/images/tryhackme/rabbitstore/room.png
---

Bom dia pessoal! Simbora com mais um desafio tryhackme!

Nmap:
```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -Pn -n -T4 -sC -sV -p- 10.10.147.29

Nmap scan report for 10.10.147.29
Host is up (0.20s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3f:da:55:0b:b3:a9:3b:09:5f:b1:db:53:5e:0b:ef:e2 (ECDSA)
|_  256 b7:d3:2e:a7:08:91:66:6b:30:d2:0c:f7:90:cf:9a:f4 (ED25519)
80/tcp    open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://cloudsite.thm/
|_http-server-header: Apache/2.4.52 (Ubuntu)
4369/tcp  open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672
25672/tcp open  unknown
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1164.35 seconds

```

![alt text](assets/images/tryhackme/rabbitstore/image1.png)

Por causa da linha: |_http-title: Did not follow redirect to http://cloudsite.thm/ -- Vamos colocar essa url no /etc/hosts:

```
nano /etc/hosts
10.10.147.29 cloudsite.thm
```

O site foi criado com essa tecnologia: https://www.smarteyeapps.com/

Nada escondido no Page Source das páginas. Formulário não funciona. Ao clicar em entrar / registrar ele nos redireciona para http://storage.cloudsite.thm/
Vou adicionar também ao /etc/hosts

```
nano /etc/hosts
10.10.147.29 cloudsite.thm storage.cloudsite.thm
```

Vamos tentar registrar e logar com um usuário:

![alt text](assets/images/tryhackme/rabbitstore/image2.png)

Nosso usuário está inativo.

Vamos observar o token jwt:

![alt text](assets/images/tryhackme/rabbitstore/image3.png)

Ao receber o token, temos o campo Subscription. Vamos tentar dar override nessa informação na hora de registrar e na hora de logar, se o back estiver mau configurado ele irá pegar esse valor e substituir.

![alt text](assets/images/tryhackme/rabbitstore/image4.png)
