---
title: 'TryHackMe: HeartBleed'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, TLS, SSL]
image:
  path: assets/images/tryhackme/heartbleed/room.png
---

## Sobre o desafio

Lendo alguns textos do tryhackme fica claro que a vulnerabilidade está relacionada com o certificado SSL/TLS do host. O código foi escrito em C e aparentemente a falha está relacionada com um tamanho arbitrário que o usuário consegue enviar para o servidor. Se o usuário enviar o tamanho 0 aparentemente ocorre um vazamento de informações do servidor.

Após abrir o link no browser me deparei com uma música legalzinha.

Logo em cima do browser, no cadeado já é possível perceber que a conexão não é segura e na informação já temos 



## Nmap Scan
```
nmap -p- -T4 <ip_alvo>

```

