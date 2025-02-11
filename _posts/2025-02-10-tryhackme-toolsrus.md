---
title: 'TryHackMe: ToolsRus'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Hydra, Nikto, Nmap, ffuf, Metasploit]
image:
  path: assets/images/tryhackme/toolsrus/room.png
---

Meu alvo é http://10.10.42.84/

Entrando no navegador temos uma mensagem dizendo que tem outras partes do site funcionando...

![alt text](assets/images/tryhackme/toolsrus/image1.png)



Ok, vamos começar com o clássico nmap rápido e em todas as portas:


```
nmap -p- -T4 <ip_alvo>

Nmap scan report for 10.10.168.149
Host is up (0.21s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
1234/tcp open  hotline
8009/tcp open  ajp13

Nmap done: 1 IP address (1 host up) scanned in 623.11 seconds

```

Eu adoraria rodar dirbuster como pede a atividade porém ele é tão lento que eu desisti. Estou rodando ffuf.

```

ffuf -u http://10.10.168.149/FUZZ -w /usr/share/wordlists/dirb/common.txt       

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.168.149/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 168, Words: 16, Lines: 5, Duration: 211ms]
.htpasswd               [Status: 403, Size: 297, Words: 22, Lines: 12, Duration: 215ms]
.htaccess               [Status: 403, Size: 297, Words: 22, Lines: 12, Duration: 215ms]
.hta                    [Status: 403, Size: 292, Words: 22, Lines: 12, Duration: 216ms]
guidelines              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 209ms]
index.html              [Status: 200, Size: 168, Words: 16, Lines: 5, Duration: 210ms]
protected               [Status: 401, Size: 460, Words: 42, Lines: 15, Duration: 211ms]
server-status           [Status: 403, Size: 301, Words: 22, Lines: 12, Duration: 244ms]
:: Progress: [4614/4614] :: Job [1/1] :: 191 req/sec :: Duration: [0:00:25] :: Errors: 0 ::
                                                                                             

```
Com isso já temos a primeira, segunda e terceira resposta.

Como eu quero descobrir a conta do bob, vou rodar um hydra.

```
hydra -l bob -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt -f 10.10.168.149 http-get /protected -t 4 -V
```

Com isso descobrimos a senha.

A pergunta da outra porta que serve serviço web pode ser resolvida com a saída do nmap.

Colocando o serviço no browser conseguimos ver a versão e responder mais outra pergunta.


Vamos escanear usando nikto.
```
nikto -h http://10.10.168.149:1234/manager/html -id bob:bubbles -o resultado.txt
```