---
title: 'TryHackMe: Rabbit Store'
author: Kertonli
categories: [TryHackMe]
tags: [Médio]
image:
  path: assets/images/tryhackme/rabbitstore/room.png
---

BOM DIA!!!

A semana foi repleta de muito estudo teórico mas estamos de volta com mais um desafio direto do forno, e dessa vez de nível médio! (O primeiro que eu vou tentar concluir com esse nível.)

Vamos começar então!

Colocando no meu navegador o IP do alvo, ele altera o ip para o seguinte texto: http://cloudsite.thm/

Explicação da AI:
```
Quando você digita um endereço IP diretamente no seu navegador e ele é redirecionado para um texto como http://cloudsite.thm/, isso pode acontecer por algumas razões:

    Configuração do Servidor: O servidor que está ouvindo nesse IP pode estar configurado para redirecionar requisições que chegam diretamente ao IP para um nome de domínio. Isso é comum em servidores onde múltiplos sites estão sendo hospedados.

    Configuração de DNS: Se esse IP estiver configurado em um serviço de DNS, pode estar associado a um domínio, e o servidor web pode estar configurado para redirecionar tráfego de IP para esse domínio.

    Regras de Reescrita: Alguns servidores web têm regras que redirecionam automaticamente os usuários para um URL desejado para padronizar o acesso ou otimizar a experiência do usuário.

    Configurações de Proxy ou Firewall: Em algumas configurações de rede, proxies ou firewalls podem redirecionar requisições, fazendo com que um IP seja convertido para um nome de domínio.
```

Não estou recebendo resposta ao colocar essa url no navegador, logo é necessário adicionar essa url e esse IP no meu /etc/hosts:

```
sudo nano /etc/hosts
(adicionar a seguinte linha:)
<ip_alvo> cloudsite.thm 
```

Na página Contact Us é possível descobrir que tecnologia criou esse site:
```
https://www.smarteyeapps.com/
```

Rodando Scan Nmap:
```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sN --top-ports=10000 cloudsite.thm
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-06 13:44 EST
Nmap scan report for cloudsite.thm (10.10.55.106)
Host is up (0.21s latency).
Not shown: 8365 closed tcp ports (reset)
PORT     STATE         SERVICE
22/tcp   open|filtered ssh
80/tcp   open|filtered http
4369/tcp open|filtered epmd


┌──(kali㉿kali)-[~]
└─$ sudo nmap -sN --top-ports=10000 cloudsite.thm
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-06 13:44 EST
Stats: 0:01:52 elapsed; 0 hosts completed (1 up), 1 undergoing NULL Scan
NULL Scan Timing: About 53.21% done; ETC: 13:47 (0:01:38 remaining)
Stats: 0:02:51 elapsed; 0 hosts completed (1 up), 1 undergoing NULL Scan
NULL Scan Timing: About 79.23% done; ETC: 13:47 (0:00:45 remaining)
Nmap scan report for cloudsite.thm (10.10.55.106)
Host is up (0.21s latency).
Not shown: 8365 closed tcp ports (reset)
PORT     STATE         SERVICE
22/tcp   open|filtered ssh
80/tcp   open|filtered http
4369/tcp open|filtered epmd

Nmap done: 1 IP address (1 host up) scanned in 221.72 seconds
                                                                                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -p22,80,4369 cloudsite.thm                
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-06 13:49 EST
Nmap scan report for cloudsite.thm (10.10.55.106)
Host is up (0.25s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3f:da:55:0b:b3:a9:3b:09:5f:b1:db:53:5e:0b:ef:e2 (ECDSA)
|_  256 b7:d3:2e:a7:08:91:66:6b:30:d2:0c:f7:90:cf:9a:f4 (ED25519)
80/tcp   open  http    Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
4369/tcp open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.79 seconds


```

Vou utilizar esse link para me ajudar: https://hacktricks.boitatech.com.br/pentesting/4369-pentesting-erlang-port-mapper-daemon-epmd

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -Pn -n -T4 -p 4369 --script epmd-info cloudsite.thm
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-06 13:57 EST
Nmap scan report for cloudsite.thm (10.10.55.106)
Host is up (0.23s latency).

PORT     STATE SERVICE VERSION
4369/tcp open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.61 seconds

```