---
layout: post
read_time: true
show_date: true
title: "Bir Developer'ın Hayatını Kolaylaştıracak 3 Prensip: KISS, YAGNI, DRY"
date: 2016-03-27 10:44:16 +0300
img: banners/brain.jpg
tags: [research]
author: Ecmel Albayrak
---

### KISS: Keep It Simple, Stupid!

Gereksiz karmaşıklıktan uzak durmayı, yazdığımız kodun bizden sonrakilerin kolaylıkla anlayabilmesini/geliştirebilmesini sağlamayı hedefleyen bir prensiptir.

![kiss](http://res.cloudinary.com/dro8cemyf/image/upload/c_scale,h_159,w_495/v1460285517/keepitsimple_rnld5x.jpg)

Bu ifade [Kelly Johnson][Johnson] tarafından kullanılmıştır ve şöyle bir açıklama getirmiştir:
> Eğer sistemlerinizi kompleks yapmak yerine onları daha basit tutarsanız sisteminiz en iyi şekilde çalışacaktır. Bu nedenle tasarımda hedef nokta basitlik olmalı ve gereksiz karmaşıklıktan kaçınılmalıdır.

<!--more-->

#### Diğer açılımları

**K**eep **I**t **S**imple **S**illy

**K**eep **I**t **S**hort and **S**imple

**K**eep **I**t **Si**mple and **S**traightforward

**K**eep **I**t **S**mall and **S**imple

### YAGNI: You Aren't Gonna Need It / Buna İhtiyacın Olmayacak

Extreme Programming(XP)'nin prensiplerinden biridir. Proje geliştirirken bazen ileride ihtiyaç olacağını düşündüğümüz bazı ekstra özellikleri şimdiden planlar ve yapmaya başlarız. Bu bizi, ileride kullanılmayacak ya da daha farklı bir şeye ihtiyaç duyacağımız bir durumla karşı karşıya bırakabilir. Bu da ihtiyaç dışı planlama ve kodlama için vakit kaybına neden olmuş olacaktır. Aynı zamanda sistem daha karmaşık hale gelecektir ki bu da KISS prensibine aykırı bir durumdur.

### DRY: Don't Repeat Yourself / Kendini Tekrar Etme

Rails'in temel prensiplerden biridir. Aynı metodlar/fonksiyonlar sistemimizde farklı yerlerde kullanılıp tekrar ediliyorsa bu prensibe uymuyoruz demektir.

Andrew Hunt ve David Thomas, "The Pragmatic Programmer" adlı kitaplarında bu prensipten şöyle söz etmişlerdir:

> Bir sistem içinde bilginin her bir parçası tek, kesin ve güvenilir olmalıdır.

Diğer bir deyişle:
> Sistemin fonksiyonel davranışları, kodun tek bir parçası içerisinde tutulmaya çalışılmalıdır.

[Johnson]: https://en.wikipedia.org/wiki/Kelly_Johnson_(engineer)
