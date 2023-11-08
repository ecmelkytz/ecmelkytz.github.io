---
layout: post
read_time: true
show_date: true
title: AngularJS Nedir?
date: 2015-08-07 21:31:00 +0200
description:
img: banners/coding.png
tags: [angularjs]
author: Ecmel Albayrak
github:
---

Google'da çalışan Misko Hevery'nin 2009 yılında şahsi bir projesi olarak başlayan AngularJS şuan açık kaynak olarak yayınlanmakta olup; yüzlerce geliştiriciye ve binlerce kullanıcıya sahip devasa bir  frontend geliştirme frameworklerinden biridir. Adındanda anlaşılacağı üzere gelişmiş bir javascript kütüphanesidir.

Aslına bakarsanız AngularJS ile Javascript ve Jquery'nin yaptığı herşeyi yapabilirsiniz. Bu noktada Anguları tercih etme meselesine gelirsek; kolaylığı, custom directive özelliği, az kod ile çok iş yapılabilmesi (“Write less do more” her ne kadar Jquery sloganı olsa da...), hızlı response sağlaması vs. şeklinde sayılabilecek bir çok güzelliği barındırmaktadır.

![angularjs](https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcRIgkmHJXLPra2-SCPAmtpn6xXWSwcvVOnrLc9hn_wV3ntJH6RZ1g)

Angularjs deyince hepsi olmamakla birlikte akla gelebilecek temel unsurlar yukarıdaki resimde mevcuttur. Hepsinden kabaca söz edip daha detaylı bilgileri başka blog girdilerinde yazmayı düşünüyorum.

- Angularda two way binding dediğimiz şey çift yönlü veri bağlama yani Model'deki değişiklikleri View'e, View'deki değişiklikleri Model'e aktarma işlemidir. Bir de one time binding var. Bunların izahını sonraya bırakıyorum.
- Angularda Template dediğimiz şeyler aslında viewlerimizdir.
- Bir sayfadan başka sayfaya geçildiğinde geri işlemi için eski state'i tutan bir hafızaya (geçmiş bilgisine) ihtiyaç vardır bunu History mekanızması sağlar.
- Factories, Services ve yukarıdaki resimde olmayan Provider Angular'da servis oluşturma komponentleridir.
- Angular bünyesinde Jquery'nin Lite versiyonu vardır. Bunu jqLite modülü ile sağlar. Eğer projenizde jquery dosyaları yok ise Angular jqlite modülünü kullanacaktır. Aksi takdirde sistemde yüklü olan jquery dosyasını kullanır.
- MVC bir design patterndir. Angular ilk başlarda MVC mantığı ile geliştirilmiş ancak daha sonrasında MVVM design patterni kullanılmıştır. Buradaki VM(ViewModel) aynı zamanda angulardaki $scope servisine denkttir.
- Angularda Directive'ler dışında DOM'a müdahale etmemek gerekiyor. Directive deyince DOM manipülasyonlarının yoğun olarak yapıldığı yer aklımıza gelmeli. Yapısal directiveler anguların kendi bünyesindeki  directivelerdir (ng-controller, ng-if, ng-show vs.). Bir de kendimizin oluşturabileceği custom directiveler vardır. Custom directiveler ile kendi Html etiketimizi oluşturup Html'e yeni özellikler ekleyebiliriz.
- Angular form validasyonlarında benimde severek kullandığım validasyon mekanizmasına sahiptir. Sunucuya gitmeden client tarafında verileri validasyona tabi tutuyoruz.
- Dependency Injection ise uygulamanın bağımlılıklarının belirtilmesidir. Mesela bir modülde third party kütüphane kullanma ihtiyacı var ise ya da farklı bir modül ile ilişkili ise bunları dependecy olarak module eklememiz gerekmektedir.

- Angular ile Single Page Application(SPA) oluşturabilirsiniz. SPA imkanıyla beraber angular route kaynaklı bazı SEO problemleri yaşanabilir. Angularda route yapısında # karakteri kullanılır.

```
#/users
#/users/new
```

Google # işaretinin gerçek bir adres karakteri olmadığını bildiği için bu karakterden sonra ne yazıldıysa yazılsın onu indexlemiyor. =(

Yukarıdaki bilgiler detaya inmeden kabaca üstünden geçilerek yazılmıştır. Bu sayfadaki bütün bilgilerin ayrıntısını ilerleyen zamanlarda yazmayı planlıyorum.

Son olarak;
Angular hızlı response oluşturur dedik. Peki nasıl?

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1448222532/browser1_pyrbaa.png)

Bir web sitesine girmeye çalıştığınızı düşünün. İlk olarak browserdan server'a URL isteğinde bulunuyoruz. Server bize cevap olarak web sayfasını ve assets (Javascript vs.)'i döndürüyor. Daha sonra kullanıcı herhangi bir linke tıkladığında, server'a yine bir istek gidiyor ve dönen cevap ilk dönen cevap ile aynı oluyor. Yani tekrar sayfanın tamamı ve assets yükleniyor.

Birde AngularJS ile bu iş nasıl oluyor ona bakalım.

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1448222636/browser2_fqhyc3.png)

Sayfa ilk yüklendiğinde geleneksel senaryo ile aynı responsu alacağız. Ancak kullanıcı bir linke tıkladığında veya başka bir yeni istekte bulunduğunda diğerinde olduğu gibi bütün bir sayfayı ve assets'i yüklemiyor. Önceden oluşturduğu veriden istenilen veriyi JSON data olarak çekiyor. Dolayısıyla istemci-sunucu trafiğini azaltmış olup hızlı bir cevap elde etmiş oluyoruz.
