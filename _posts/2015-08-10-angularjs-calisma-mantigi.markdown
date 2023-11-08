---
layout: post
read_time: true
show_date: true
title: AngularJS Çalışma Mantığı
date: 2015-08-10 23:26:37 +0200
img: banners/adventure.jpg
tags: [angularjs, coding]
author: Ecmel Albayrak
description:
---

Angularjs ile ilgili beni en çok uğraştıran konu kesinlikle angularjs'in çalışma mantığını anlatmak olacak. Çünkü Angularjs ng-app directive'i gibi basit bir söz dizilimi ile çalışmaya başlayıp, mekanizmayı tam anlamıyla anlamak için içerisine dallandıkça **digest cycle**,  **$watch list**, **two way binding**, **dirty cheking** mekanizmasının birbiri ile alakasının anlatılması gereken bir konu haline geliyor.

Eğer bir Angularjs uygulaması oluşturmak istiyorsanız elbetteki ilk yapacağınız iş anguların kütüphanesini projenize eklemek olacaktır. Angularjs'nin sayfanın başına veya sonuna eklenmesinin bir önemi yok çünkü Angularjs uygulaması DomReady olmadan yani tüm scriptler yüklenmeden boot etmiyor.

Angularjs uygulamasını başlatmak için `ng-app` yapısal directive ihtiyacımız vardır. Angularjs yüklendikten sonra uygulamanın başlaması için aranan ilk directive `ng-app`'dir. `ng-app`'ın bulunduğu etiketin sarmaladığı alanlar arasında Angular uygulaması başlar.

```html
<!DOCTYPE html>
<html ng-app>
  <head>
    <script src="https://code.angularjs.org/1.3.9/angular.js"></script>
  </head>
<body></body>
</html>
```

Yukarıdaki kodda `ng-app` html etiketine eklendiği için bu taglar arasında kalan yer Angularjs uygulaması olarak çalışacaktır. `ng-app` direktive'i aynı zamanda uygulamanın global scope'u olan `$rootScope` nesnesini oluşturmaktadır.
Yeri gelmişkende belirteyim,  `ng-app`'in kullanımı W3C standartlarına uymadığı için `data-ng-app` kullanımı tavsiye edilmektedir.

`ng-app` directive'inden sonra sayfa içerisindeki diğer directiveler aranmaya başlanır. Bulunan direktiveler uygulamaya bind edilir.  Bind edilenler `$wacth list`'e eklenir. Daha sonrasında digest cycle kullanıcı etkileşimlerini dinlemeye başlar.

#### Digest Cycle

Digest Cycle uygulamanın view katmanını yani kullanıcının gördüğü ekrandaki verileri güncel tutmakla görevlidir. Kullanıcı tarafından yapılan en ufak bir etkileşimde view'in otomatik güncellenmesini sağlar.

Özetle; Digest Cycle kullanıcı etkileşimlerini dinliyor, bir değişiklik var ise view kısmını güncel tutuyor vs. tamam ama bunun sağlanmasında arkada nasıl bir mekanizma çalışıyor asıl buna değinmek ve biraz daha derine inmek istiyorum. Bu olayı two way binding üzerinden anlatacağım.

#### Two Way Binding denilen şey ne, nasıl işler?

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1449136639/desktop-animation2_av1hak.gif)

Yukarıdaki gif görsel olarak two way binding dediğimiz şeydir. Görüldüğü üzere model içerisinde yaptığım her değişiklik bind ettiğim view kısmına anında aktarılıyor. [Plunker Code](http://plnkr.co/edit/ayo5ZxwxmKeptLTSxElu?p=preview)

```html
<body ng-app>
  <input type="text" ng-model="name">
  <h1> Merhaba {% raw %}{{ name }}{% endraw %} </h1>
</body>
```

{% raw %}{{ }}{% endraw %} işareti angular'da expression olarak adlandırılır ve scope nesnesini view'e bind (bağlamaya) etmeye yarar.

Şimdi elimizde bir `$watch list`  adında bir listemizin olduğunu hayal edelim. Biz UI'ye her bind ettiklerimizi bu `$watch list`'e  `$watch` nesnesi olarak eklediğimizi düşünelim. Digest cycle kullanıcı etkileşimlerini dinlerken her defasında bu  `$watch list`'e bakar.  Eğer bu `$watch`'lardan birinde herhangi bir değişiklik var ise model-view senkronizasyonunu sağlar (two way binding'de olduğu gibi). Bu senkronizasyonu dirty cheking mekanizması ile yapar.

**Dirty Cheking Mekanizmasi:** Bir değerin eski değeri ile yeni değerinin karşılaştırılmasıdır.

**$watch list oluşturma örneği 1:**

```js
app.controller('MainController', function($scope) {
  $scope.foo = "Foo";
  $scope.bar = "Bar";
});
```

```html
  Hello {% raw %}{{ bar }}{% endraw %}
```

Yukarıdaki örnekte html sayfasında ekran çıktısı olarak “Hello Bar” yazısı gözükecektir. Çünkü {% raw %}{{ bar }}{% endraw %} expression'ı scope nesnesini view'e bind edecek. Controller içinde scope'a tanımlanmış bir de foo değişkeni var ancak o view'e bind edilmemiş durumda. İşte bu yüzden `$watch list`'e sadece 1 tane $watch nesnesi eklenecek. Digest cycle ile de sadece `$scope.bar` üzerindeki değişiklikler dinlenecek ve view'e aktarılacak.

**$watch list oluşturma örneği 2:**

```html
  Name: <input type="text" ng-model="name" />
  Password: <input type="password" ng-model="password" />
```

`ng-model` directivenin özelliklerinden birisi input nesnesini scope’a bağlamaktır. Yani $scope.name ve $scope.password'un ikiside bind edildiği için `$watch list`'e 2 tane $watch nesnesi eklenecek ve digest cycle bu iki inputtaki değişiklikleri dinleyecek.

**Ufak bilgi:** Digest çevirimi tetiklendikten sonra minimum 2 maximum 10 kez çalışır. (Angular 1.4’de 20 kez)

**Digest Loop `$watch list` gezerken nasıl bir senaryo izliyor biraz daha bakalım:**

> - Hey $watch, senin değerin ne?
>   - 9
> - Pekala, bir değişiklik var mı? (Dirty Checking)
>   - Hayır yok.
>
> (Herhangi bir değişiklik olmadığı için diğer $watch’a geçer.)
> - Hey sen, senin değerin ne?
>   - Foo
> - Bir değişiklik var mı?  (Dirty Checking)
>   - Evet, önceden Bar’dım.
> - Güzel, o halde DOM’u güncelleyelim.

`$watch`’lar bitene kadar bu çevirim devam eder. $digest loop bittiğinde değişiklik var ise DOM'da değişiklik yapılır.

AngularJS çalışma mantığını elimden geldiğince anlatmaya çalıştım. Velhasıl-ı kelam faydalı olması dileğiyle...
