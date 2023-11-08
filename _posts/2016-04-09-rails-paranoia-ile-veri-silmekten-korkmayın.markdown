---
layout: post
read_time: true
show_date: true
title: "Rails: Paranoia ile Veri Silmekten Korkmayın"
date: 2016-04-09 13:58:21 +0300
img: banners/keyboard.jpg
tags: [coding, rails]
category:
author: Ecmel Albayrak
---

<iframe src="//giphy.com/embed/bEVKYB487Lqxy" width="480" height="264" frameBorder="0" class="giphy-embed" allowFullScreen style="display: block;margin: 0 auto;margin-bottom:20px"></iframe>

> “Bu veriyi silersem ileride lazım olur mu?”
>
> “Eyvah! Yanlış veriyi sildim şimdi ne yapacağım?"

tarzında çeşitlendirilebilen kaygılarınız var ise paranoia gemi imdadınıza yetişecektir.

### Peki nedir bu paranoia ve nasıl kullanılır?

Paranoia silme işleminde `soft delete` yapmamıza olanak sağlar. Yani veriyi sildiğimiz zaman aslında tam anlamıyla veritabanından o veriyi yok etmiş olmuyoruz.

Tabloya type'ı datetime olacak şekilde ekleyeceğimiz `deleted_at` alanı sayesinde silme işlemi yaptığımız zaman sadece bu kolonun değeri güncellenecek ve silinme zamanı burada tutulacak.

Bu plugin Active Record'un sadece destroy metodu üzerinde override yapmaz aynı zamanda find ve count metodları üzerinde de değişiklik yapar. Sql sorgularına deleted_at'i null olanların getirilmesiyle ilgili eklemeler getirecektir.

Migration ile modelimize `deleted_at` alanını ekleyelim:

```ruby
$rails generate migration AddDeletedAtToUsers deleted_at:datetime:index
```

Migration dosya içeriğimiz:

```ruby
class AddDeletedAtToUsers < ActiveRecord::Migration
  def change
    add_column :users, :deleted_at, :datetime
    add_index :users, :deleted_at
  end  
end
```

```ruby
class User < ActiveRecord::Base
  acts_as_paranoid
  ...
end
```

Şimdi bir user silelim:

```ruby
> User.count
# => 2

> User.last.destroy
# deleted_at güncellenecektir.

> User.count
# => 1

# Silinen veriler ile birlikte hepsini bulmak için:
> User.with_deleted

# Sadece silinen verileri bulmak için:
> User.only_deleted
```

`User.count` kullandığımızda veya `find` ile o user'ı bulmaya çalıştığımızda user silinmiş gibi davranacaktır ancak veritabanında users tablosuna baktığımız zaman bu silinmiş sandığımız kaydın hala orada durduğunu ve `deleted_at` alanının verinin silindiği zamanı tuttuğunu göreceğiz.

Peki paranoia ile hiçbir zaman veritabanından bir veriyi tam anlamıyla silemeyecek miyiz? Bunun da bir çözümü var. Herşeye rağmen kesinlikle silmek istediğimiz kayıtlar var ise:

```ruby
 $User.last.really_destroy!
```

Eğer veritabanında tutacağımız kolon adının `deleted_at` yerine `destroyed_at` (veya herhangi birşey) olmasını istiyorsak:

```ruby
class User < ActiveRecord::Base
  acts_as_paranoid column: :destroyed_at
  ...
end
```

Callback'ler ile kullanımı ve paranoia hakkında daha fazla bilgi için [bakınız][paranoi].

[paranoi]: https://github.com/rubysherpas/paranoia
