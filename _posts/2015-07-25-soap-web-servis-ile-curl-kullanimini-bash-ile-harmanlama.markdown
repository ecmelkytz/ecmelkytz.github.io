---
layout: post
read_time: true
show_date: true
title: SOAP Web Servis ile CURL Kullanımını Bash ile Harmanlama
date: 2015-07-25 11:37:26 +0200
description:
img: banners/icons.png
tags: [coding, bash]
author: Ecmel Albayrak
---


Terminalden IP öğrenme ve IP adresi sorgulamak (x IP kullanılıyor mu, hangi ülke tarafından kullanıyor vs.) için yazılmış basit bir bash kodudur. Curl ile web servis kullanımını bash ile harmanlar. Can sıkıntısı ürünüdür.

### Özetle: Atla deve değildir.

Dosya içeriği:

```bash
#!/bin/bash

GETGEOIPCONTEXT="http://www.webservicex.net/geoipservice.asmx/GetGeoIPContext"
GETGEOIP="http://www.webservicex.net/geoipservice.asmx/GetGeoIP"


get_ip() {
  curl -H "Accept: application/soap+xml" -d "IPAddress=$1" ${GETGEOIP}
}

my_ip() {
  curl -H "Accept: application/soap+xml" -d "IPAddress=$1" ${GETGEOIPCONTEXT}
}

$@
```

Terminal'de kullanım:

```bash
$ geoip my_ip
# Ekran çıktısı
  <ReturnCode>1</ReturnCode>
  <IP>193.xxx.xx.xx</IP>
  <ReturnCodeDetails>Success</ReturnCodeDetails>
  <CountryName>Turkey</CountryName>
  <CountryCode>TUR</CountryCode>
```

```bash
$ geoip get_ip 195.xxx.xx.xx
# Ekran çıktısı
  <ReturnCode>1</ReturnCode>
  <IP>195.xxx.xx.xx</IP>
  <ReturnCodeDetails>Success</ReturnCodeDetails>
  <CountryName>Germany</CountryName>
  <CountryCode>DEU</CountryCode>
```

Yazdığımız bash kodunu konsoldan çalışır duruma getirme işine bakalım:

```bash
$chmod 755 geoip
```

Artık kodun bulunduğu dizinde şu şekilde çalıştırabiliriz.

```bash
$./geoip my_ip
```

Sadece dosya ismiyle çalıştırmak istiyorum. Bundan dolayı sudo ile mc (Midnight Commander) aracılığıyla bash script'i /bin dizinine attım. Aynı işlemi yapabileceğimiz bir çok yol elbette mevcut.

Misal:

```bash
# Taşımak için

$ sudo mv path/geoip bin

# Kopyalamak için

$ sudo cp path/geoip bin
```

[Terminal'de kullanım] kısmında gözüktüğü gibi kodumuz çalışır hale gelecektir.

![big-deal](http://res.cloudinary.com/dro8cemyf/image/upload/v1457516071/bigdeal_bpnp2u.jpg)
