---
layout: post
read_time: true
show_date: true
title: Git Komutları
date: 2016-08-11 10:39:24 +0300
img: banners/devices.jpg
tags: [git, github]
category:
author: Ecmel Albayrak
---

#### git diff

Kendi yerelimizde yaptığımız değişiklikler neticesinde, projeyi yönlendirdiğimiz repo arasında oluşan farkları gösterir. Hangi satırı sildiğimiz, hangi satırı eklediğimiz vs. bilgilerini içerir.

```bash
~/isy$ git diff  #Yapılan bütün değişiklikleri gösterecektir.
```

Yapılan bütün değişiklikleri değil de sadece istediğimiz dosya içerisinde nasıl bir değişiklik yapıldığını görmek için o dosyanın adını `git diff` komutunun yanına eklemeliyiz.

```bash
~/isy$ git diff README.md #README.md dosyasında yapılan değişiklikleri gösterir.
```

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1470987741/diff_nhrmdn.png)

<!--more-->

#### git blame

Belirlenen dosyadaki her bir satırın hangi commit ile eklendiği, kimin tarafından ve ne zaman eklendiği bilgilerini gösterir.

```bash
~/isy$ git blame README.md
```

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1470990914/git_blame_phsyrz.png)

#### git checkout

Dosyalarda yapmış olduğumuz değişiklikleri geri alır. Bu komut ile yapılan değişiklikleri geri aldığımız için `git status` komutunun ekran çıktısında dosyamızın ismi artık gözükmeyecektir.

```bash
~/examp-git$ git checkout README.md #README.md dosyasındaki değişiklikleri siler.
~/examp-git$ git checkout . #Yapılan bütün değişiklikleri geri alır.
```

`git checkout HEAD~` komutu ile projemizi istediğimiz commit durumuna geri döndürebiliriz. Şöyle ki: Projemizin bulunduğu dizinde `git log` komutunu çalıştırdığımız zaman en son commitden en baştaki committe doğru bütün commitler sıralanacaktır.

```bash
~/isy$ git log
commit 898de0b8f2d21497569ed980c990d32778e7bd5c
Author: ecmel <ecmel.kytz@gmail.com>
Date:   Tue Jul 26 08:57:38 2016 +0300

    Hava durumuna yeni açıklama ekle

commit 1985f16aa89d41fae740086c53fb1ecefca4affc
Author: ecmel <ecmel.kytz@gmail.com>
Date:   Wed Jul 20 12:47:44 2016 +0300

    Tasarımsal düzenlemeler

commit d44645020926795a4f6b9d74c279632d344543ec
Author: ecmel <ecmel.kytz@gmail.com>
Date:   Wed Jul 20 09:20:50 2016 +0300

    Excel dosya isimlerinde düzenleme yap
```

En son commit "Hava durumuna yeni açıklama ekle" iken ben "Excel dosya isimlerinde düzenleme yap" commiti durumuna geçmek istiyorsam çalıştırmam gereken kod: `git checkout HEAD~2`

Geçmek istediğim commit en son commitden(master) kaç commit gerideyse `HEAD~` yanına o rakamı koyuyoruz. Sistemi tekrar son haline geri döndürmek için ise `git checkout master` yazmamız yeterli.

#### git clean

`git checkout` ile dosyalarda yapılan değişiklikleri geri alabiliyorduk. `git clean` ile ise yeni eklenen dosyaları silebiliyoruz.

```bash
~/isy$ git clean -fd #Bütün eklenen yeni dosyaları siler.
~/isy$ git clean new_file.txt #new_file adında yeni eklenen dosyayı siler.
```

#### git reset

- Söz gelimi yaptığımız değişiklikleri `git add .` komutu ile izlemeye aldık ve hepsini izlemeden çıkarmak istiyoruz, o halde `git reset` komutunu kullanmamız gerekmektedir. İzlenen bütün dosyaları değil de sadece bazı dosyaları izlemeden çıkarmak için: `git reset HEAD file_name` komutu çalıştırılmalı.

- Yapılan değişiklikler için commit mesajını yazdık ve github'a göndermeden commit mesajını değiştirmek istiyorsak şu komut çalıştırılmalı:

```bash
~/isy$ git reset --soft HEAD~
```

Bu komut izlemeyi kaldırmayacaktır. `git commit -m ""` diyerek yeni commit mesajımızı girebiliriz.

- Github'a gönderdiğimiz son commiti geri almak için:

```bash
~/isy$ git reset (kalmasını istediğin commit numarası ör:def668e)
git push -f
```

Bu komutu kullanırken dikkatli olmak gerek zira referans aldığınız commit numarasından önceki bütün commitleri silersiniz. Yani sistemi, referans aldığınız commit durumuna almış olursunuz ve ondan önceki bütün commitleri silersiniz. Silinen commitler ile yapılan değişiklikler sizin yerelinizde gözükür ve tek commit ile tekrar `push` edebilirsiniz. Çok gerekmediği takdirde birden fazla commiti geri alıp tek commit ile tekrar göndermek projenin gelişim aşaması için anlaşılmazlıklara sebebiyet verebilir. Bu sebeple ben genel itibariyle son attığım commiti değiştirmek için kullanıyorum bu komutu.

#### git stash

En sevdiğim kodlardan bir tanesi. Kısacası yaptığımız değişiklikleri geçici olarak depolamamızı sağlar.

![](http://res.cloudinary.com/dro8cemyf/image/upload/v1471261268/stash_ewoovx.png)

Şöyle bir senaryo düşünelim: Uygulamamızdaki bir çok dosyada değişiklik yaptık, yeni dosyalar ekledik vs. ve aniden bir iş çıktı, o işi yapıp biran önce commit etmemiz gerekti. İşte bu tarz durumlarda hangi dosyaları göndereceğinizi eliyip seçmek yerine `git stash` komutu ile önceden yaptığımız değişiklikleri kaydedip(artık `git status` ile gözükmeyecekler) acil yapmamız gereken işi yapıp commitleyip eski işimize tekrar dönebiliriz.

```bash
~/isy$ git stash
Saved working directory and index state WIP on master: 898de0b Hava durumuna yeni açıklama ekle
HEAD is now at 898de0b Hava durumuna yeni açıklama ekle
```

Acil durum düzenlemelerini yaptıktan sonra yedeklediğimiz diğer işimize şu şekilde dönüyoruz:

```bash
~/isy$ git stash pop
```

#### .gitignore

Yapılan değişikliklerin takip edilmesini istemediğimiz dosyaları/dosya yollarını `.gitignore`'un içerisine ekliyoruz. Ancak `.gitignore`'a sonradan eklenen dosya yolları `git status` ile değişiklik yapılanlar arasında gösterilir. Bu durumu düzeltmek için şu yol izlenir:

```bash
~/isy$ git rm -r --cached .
~/isy$ git add .
~/isy$ git commit -m ".gitignore artık düzgün çalışıyor"
```

Bu komutların yaptığı şey tam olarak şöyle: Takibini yapmasını istemediğiniz dosyalar remote ettiğimiz yerdeki repo'da mevcut olduğu için o dosyaların silinmesini sağlar. O dosyaları sildikten sonra artık yaptığınız değişiklikler izlenmeyecek ve `git status` ile görüntülenmeyecektir.

`Repo'daki dosyalar silinmesin ve yaptığım değişiklikler izlenmesin`'i istiyorsak şu işimizi görecektir:

```bash
git update-index --assume-unchanged FILE_NAME
```
