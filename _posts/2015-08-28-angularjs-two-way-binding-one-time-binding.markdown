---
layout: post
read_time: true
show_date: true
title: Two Way Binding - One Time Binding In AngularJS
date: 2015-08-28 22:31:15 +0200
img: banners/codes.png
tags: [coding, angularjs]
category:
author: Ecmel Albayrak
description:
---
![data-binding](https://docs.angularjs.org/img/guide/concepts-databinding1.png)

Two way binding (çift yönlü veri bağlama) modeldeki değişiklikleri view'e, view'deki değişiklikleri modele aktarma işlemidir. Daha önceki [yazımda](http://ecmelkytz.github.io/blog/2015/08/11/angularjs-calisma-mantigi/) two way binding ve doğal olarak digest cycle'dan uzun uzadıya bahsetmiştim.

Two way binding, kullanıcı etkileşimlerinin direk view kısmına aktarılması kısmında çok güzel bir işleve sahip olsa da; digest cycle tetiklendikten sonra en az 2, maksimum 10 kez çalıştığı düşünülürse performans anlamında büyük sorunlar oluşturacaktır. Şöyle ki; benim data binding sayım arttığı ölçüde $digest loop'un sayısı da artacaktır. Kullanıcı etkileşiminin yoğun olduğu bir sayfada performans anlamında yaşanabilecek sıkıntılar elbette ki kaçınılmaz olacaktır.

Hal böyle olunca Angularjs geliştiricileri Angular 1.3 versiyonu ile data binding'den kaynaklanan performans sorunlarını gidermek adına one-time binding'i tanıttılar. One time binding view-model güncelleme işlemini bir kez yapıyor, bundan sonra yapılan etkileşimler herhangi bir şekilde view'e ya da modele yansıtılmıyor. Yani digest cycle bir kez kullanıcı etkileşimlerini dinliyor ve bu sayede güncelleme bir kez yapılmış oluyor.

### One-time binding nasıl çalışır görelim

![one-time-binding](http://res.cloudinary.com/dro8cemyf/image/upload/v1449581533/one-time-binding_eaj5td.gif)

Yukarıdaki gif'den de anlaşılacağı üzere model'de yazdığım yazının ilk karakteri view'e bağlanıyor. Ondan sonraki etkileşimlerde hiçbir şekilde veri bağlama yapılmıyor.

### Syntax farkına bakalım

Two way binding

```html
  <p> {% raw %}{{ name }}{% endraw %} </p>
```

One time binding

```html
  <p> {% raw %}{{ ::name }}{% endraw %} </p>
```
