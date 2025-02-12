---
title: 'TryHackMe: ToolsRus'
author: Kertonli
categories: [TryHackMe]
tags: [Fácil, Ohsint]
image:
  path: assets/images/tryhackme/ohsint/room.png
---

## O desafio

Retirar o máximo de informações de uma imagem. Vamos usar exiftool.

```

exiftool -a -G -s ohsint.jpg        
[ExifTool]      ExifToolVersion                 : 13.10
[File]          FileName                        : ohsint.jpg
[File]          Directory                       : .
[File]          FileSize                        : 234 kB
[File]          FileModifyDate                  : 2025:02:12 14:57:05-03:00
[File]          FileAccessDate                  : 2025:02:12 14:59:04-03:00
[File]          FileInodeChangeDate             : 2025:02:12 14:57:05-03:00
[File]          FilePermissions                 : -rw-rw-r--
[File]          FileType                        : JPEG
[File]          FileTypeExtension               : jpg
[File]          MIMEType                        : image/jpeg
[File]          ImageWidth                      : 1920
[File]          ImageHeight                     : 1080
[File]          EncodingProcess                 : Baseline DCT, Huffman coding
[File]          BitsPerSample                   : 8
[File]          ColorComponents                 : 3
[File]          YCbCrSubSampling                : YCbCr4:2:0 (2 2)
[XMP]           XMPToolkit                      : Image::ExifTool 11.27
[XMP]           GPSLatitude                     : 54 deg 17' 41.27" N
[XMP]           GPSLongitude                    : 2 deg 15' 1.33" W
[XMP]           Copyright                       : OWoodflint
[Composite]     ImageSize                       : 1920x1080
[Composite]     Megapixels                      : 2.1
[Composite]     GPSLatitudeRef                  : North
[Composite]     GPSLongitudeRef                 : West
[Composite]     GPSPosition                     : 54 deg 17' 41.27" N, 2 deg 15' 1.33" W

```

Pegando a linha do Copyright e pesquisando, obtemos diversas informações do github, commits do github, site do wordpress e twitter.

![alt text](assets/images/tryhackme/ohsint/image3.png)

![alt text](assets/images/tryhackme/ohsint/image4.png)

![alt text](assets/images/tryhackme/ohsint/image5.png)

Para responder a pergunta do Bssid eu decidi usar o site: https://wigle.net/.

![alt text](assets/images/tryhackme/ohsint/image2.png))

A senha dele está escondida no html do blog do wordpress.

![alt text](assets/images/tryhackme/ohsint/image1.png)