---
layout: post
read_time: true
show_date: true
title: AngularJS Single Page Application
date: 2015-08-15 22:04:08 +0200
img: banners/laptop.webp
tags: [coding, angularjs]
category: 
author: Ecmel Albayrak
description:
---

Single page application (SPA), tek bir sayfada sunulan uygulamaların genel adıdır. SPA ile sayfa içinde gezinebilmemiz onu ayrı sayfalara bölmez.

Angularjs ile bir single page application nasıl oluşturuluyor. Angular bunun için nasıl bir route mekanizması kullanıyor ona bakalım.

```js
var app = angular.module('myApp', ['ngRoute']);

app.config(['$routeProvider', function($routeProvider) {
  $routeProvider.
    when("/home", {
      templateUrl: 'home.html'
    }).
    when("/services", {
      templateUrl: 'services.html'
    }).
    when("/clients", {
      templateUrl: 'clients.html'
    }).
    otherwise({
      redirectTo: '/home'
    });
}]);
```

Uygulamamızın modul'une ngRoute modul'unu dependency olarak belirtiyoruz. Yani modulun bağımlılıklarını belirtiyoruz. Bu sayede ngRoute içindeki her fonksiyonu/servisi kendi uygulamamızda da kullanabiliyoruz.

Config sadece Provider'lar için kullanılır. Provider'lar Angularjs'de en gelişmiş servis oluşturma kompanentidir. Buradaki ``$routeProvider`` servisi uygulama başlamadan Routing konfigürasyonunu sağlar.

`$routeProvider` sayesinde eğer url isteği /home olursa template olarak *home.html* kullanılmasını, eğer istek /services olursa template olarak *services.html* kullanılmasını sağlıyoruz. `.otherwise` ise olmayan herhangi farklı bir istekte bulunulursa /home url'e yönlendirmek için kullanılmaktadır.

```html
<h1>Services Page</h1>
<p>Nobody can give you freedom. If you're a man, you take it.</p>
```

Angular route yapısı için daha çok [ui-router](https://github.com/angular-ui/ui-router) kullanılması tavsiye edilir.

```html
<html ng-app="myApp">

  <head>
    <link rel="stylesheet" href="style.css">
    <script src="https://code.angularjs.org/1.3.9/angular.js"></script>
    <script src="https://code.angularjs.org/1.3.9/angular-route.js"></script>
    <script src="script.js"></script>
  </head>

  <body>
    <ul>
      <li><a href="#/home">Home</a></li>
      <li><a href="#/services">Services</a></li>
      <li><a href="#/clients">Clients</a></li><br>
    </ul>
      <div ng-view></div>
  </body>

</html>
```

Angularjs route yapısında `#` karakteri kullanılır. O yüzden linklerdeki url'lerde `#` karakteri kullanılmıştır. `ng-view` aslında Rails'de ki `yield` ile aynı işlevi görür. Angularda eğer bunu eklemezsek sayfada görüntü oluşmayacaktır.

Angular Single Page Application örneğinin [plunker adresi](http://plnkr.co/edit/BrCnZCuCuT2b7ZdkTju1?p=preview).
