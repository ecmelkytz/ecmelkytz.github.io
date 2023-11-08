---
layout: post
read_time: true
show_date: true
title: "Rails: default_scope Kullanmak Kötü Bir Fikir mi?"
date: 2017-03-12 15:27:27 +0300
img: banners/galaxy.jpg
tags: [coding, rails]
category:
author: Ecmel Albayrak
---

`default_scope` belirli bir model üzerinde yapılacak tüm işlemler için varsayılan bir kapsam belirtmemizi sağlayan ActiveRecord metodudur.

Bu scope'u(kapsamı) modelimizde soft-deletion işlemi yapmak için kullanabiliriz. Soft-delete işlemi için [paranioa](https://github.com/rubysherpas/paranoia) kullanmanızı ayrıca öneririm. [Acts_as_paranoid ile veri silmekten korkmayın](http://ecmelkytz.github.io/rails-paranoia-ile-veri-silmekten-korkmayın.html) başlıklı blog girdimde bundan bahsetmiştim.

Aşağıdaki gibi `deleted_on` kolonunu kullanarak default bir scope oluşturalım.

```ruby
class Animal < ActiveRecord::Base
  # Eğer bir veri silindiyse `deleted_on` alanı dolu olacak.
  default_scope where(deleted_on: nil)
end
```

**Animal** modelinde tanımladığımız `default_scope` ile silinen kayıtları saklamış ve sadece silinmemiş olan kayıtlarla iş yapmamızı sağlamış olacağız.

```ruby
> Animal.limit(3)
=> Animal Load (4.2ms)  SELECT `animals`.* FROM `animals` WHERE `animals`.`deleted_on` IS NULL LIMIT 3

```

Bu scope tanımlaması bazı senaryolar için gerçekten kullanışlı bir durum olabilir ancak `default_scope` kullanımının tehlikeli bir yanı olduğunu da gözardı etmemek gerek. Çünkü bu scope modelimizin (initialization) başlatılmasını etkiler. Yani tüm (all) model kayıtlarımız üzerinde işlem yapmayı engellemiş oluruz.

Bir örnek görelim:

**Animal** modelimizin polymorfic bir ilişkiye sahip olduğunu farzedelim ve `default_scope` ile  her zaman type'ı **Cat** olacak şekilde filtreleyelim.

```ruby
class Animal ActiveRecord::Base
  belongs_to :genus, polymorphic: true
  default_scope where(genus_type: 'Cat')
end
```

Yeni bir **Animal** oluşturmaya çalıştığımız zaman `genus_type`'ı varsayılan olarak **Cat** olacaktır.

```ruby
> Animal.new
=> <Animal id: nil, name: nil, genus_id: nil, genus_type: "Cat", created_at: nil, updated_at: nil>
```

Bu tarz yan etkiler bilinmeden/farkına varılmadan kullanılırsa can sıkıcı problemlerin yaşanılması kaçınılmaz olacaktır.

Ayrıca `default_scope` kullandıktan sonra devam sorgularının yazılmasının da zor olduğunu belirtmek isterim.

Örneğin modelimizde type'ı **Cat** olmayanları şu şekilde bulmaya çalışalım:

```ruby
> Animal.where.not(genus_type: 'Cat')
```

Bu query ile istediğimiz sonucu elde edemeyeceğiz. Çünkü default olarak sadece type'ı **Cat** olanlar baz alınarak query başlatılacaktır. Önceden de bahsettiğim gibi burada initialization'ı etkilemiş oluyoruz. Peki diğer type'lara sahip kayıtlara hiç mi ulaşamayacağız? Elbette bunun da bir yolu var. Bunu sağlamak için ek olarak `unscope/unscoped` kullanmamız gerekecek.

#### ActiveRecord kapsamlarını nasıl sileriz?

Aşağıdaki kapsamlara sahip bir User modelimiz olsun.

```ruby
class User < ActiveRecord::Base
  scope :active, -> { where(locked: false) }
  scope :admins, -> { where(role: 'admin') }
  scope :ordered, -> { order(:name) }
end
```

```ruby
# Tüm kapsamları kullandık
> users = User.active.admins.ordered
=> SELECT "users".* FROM "users" WHERE "users"."locked" = 'f' AND "users"."role" = 'admin' ORDER BY "users"."name" ASC
```

Kapsamları kaldırmak için `unscope` ve `unscoped` metodlarını devreye sokalım:

```ruby
# Tek bir kolon üzerindeki koşulları kaldırmak için
> users.unscope(where: :role)
=> SELECT "users".* FROM "users" WHERE "users"."locked" = 'f' ORDER BY "users"."name" ASC

# Bütün koşulları kaldırmak için
> users.unscope(:where)
=> SELECT "users".* FROM "users" ORDER BY "users"."name" ASC

# Order kaldırmak için
> users.unscope(:order)
=> SELECT "users".* FROM "users" WHERE "users"."locked" = 'f' AND "users"."role" = 'admin'

# Bütün kapsamları kaldırmak için
> users.unscoped
=> SELECT "users".* FROM "users"
```

Bir diğer dikkat etmemiz gereken husus `unscoped` kullanımı olacak. Çünkü bu query sadece default kapsamları değil tanımlı bütün kapsamları kaldıracaktır. `unscoped` Rails 3'den beri mevcut ancak `unscope` Rails 4 ile geldi.

Vel hasıl-ı kelam kullanacağımız `default_scope`'un kapsamını, uzun vadedeki etkilerini biliyorsak kullanmamızda bir sakınca olmayacaktır.
