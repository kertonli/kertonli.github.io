---
title: 'TryHackMe: Library'
author: Kertonli
categories: [TryHackMe]
tags: [          ]
image:
  path: assets/images/tryhackme/library/image.png
---

ROTEIRO

rustscan em todas as portas
22 e 80 abertas
nada de util na 80, fora um formulario que nao tras um retorno legal.
enumerando com feroxbuster
hydra no ssh com usuario meliodas -> bingo
posso executar arquivo bak.py
nao consigo editar ele porém eu sou o dono do diretório entao é só deletar e criar um novo arquivo com mesmo nome.

finalizado