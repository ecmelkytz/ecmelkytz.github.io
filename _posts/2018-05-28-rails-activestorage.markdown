---
layout: post
read_time: true
show_date: true
title: "Rails: ActiveStorage"
date: 2018-05-28 11:16:46 +0300
img: banners/clouds.jpg
tags: [coding, rails, research]
category:
author: Ecmel Albayrak
---

## 1. ActiveStorage Nedir?

Rails 5.2 ile gelen ActiveStorage özelliği, dosyaları Amazon S3, Google Cloud, Microsoft Azure Cloud gibi cloud bir servise upload etmemizi sağlar.

## 2. Kurulum

`rails active_storage:install`

Bu çalıştırılan kod `active_storage_blobs` ve `active_storage_attachments` tablolarını oluşturan bir migration dosyası ve servis konfigürasyonları için config altında **storage.yml** dosyası oluşturur.

![activestorage](http://drive.google.com/uc?export=view&id=1_yLpznYWjIT29iiWDXgKgd3zEXW8gdR2)

## 3. ActiveStorage'ın Temel Sınıfları

- ActiveStorage::Service
- ActiveStorage::Blob
- ActiveStorage::Attachment

### 3.1 ActiveStorage::Service

**Service** sadece baytların hareketinden sorumlu kısımdır. Dosyanın baytlarını bellekten ve browserdan alıp disk'e taşır.

```ruby
class ActiveStorage::Service
  def upload(key, io, checksum: nil)
    raise NotImplementedError
  end
  def download(key)
    raise NotImplementedError
  end
end
```

ActiveStorage::Service aslında bir **pattern**dir. Sadece bir tane service değil, birden fazla subclass barındırır. Yani ActiveStorage::Service'i direk çağırıp **upload/download** metodlarını kullanamayız. Onun subclass'larından biriyle çağırabiliriz. Tüm alt sınıflarda upload ve download metodu mevcut çünkü her servis kendi gerçeklemesini farklı bir yol ile yapmaktadır.

Disk alt sınıfı:  `class ActiveStorage::Service::DiskService < Service`

S3 alt sınıf: `class ActiveStorage::Service::S3Service < Service`

Farklı bir servis kullanmak istiyorsak bu pattern doğrultusunda oluşturup kullanabiliriz. ActiveStorage şu an Disk, Amazon S3, Microsoft Azure Storage, Google Cloud Storage, Mirror servislerini desteklemektedir.

<!--more-->

#### 3.1.1 Yeni Servis Konfigürasyonu

**storage.yml** içerisinde default servis olarak Disk kullanılmaktadır. Hangi servisi kullanmak istiyorsak onu belirtmeliyiz. Örneğin Amazon S3 servisini eklemek için aşağıdaki gibi bir düzenleme yapmamız ve [aws-sdk-s3](https://github.com/aws/aws-sdk-ruby) gem'ini Gemfile'a eklememiz gerekmektedir.

```yml
local:
  service: Disk
  root: <%= Rails.root.join('storage') %>

amazon:
  service: S3
  access_key_id: ""
  secret_access_key: ""
  region: ""
  bucket: ""
```

#### 3.1.2 Mirror Servis Özelliği

Birden fazla servisi senkronize olarak mirror servis olarak kullanarak yedekli çalışabiliriz. Bir dosya eklendiğinde veya silindiğinde bütün mirror edilmiş servislere aynı anda yansıtılır. Bu özellik production'da servisler arası geçişi kolaylaştırmak için kullanılabilir.

Mirror servis örneği:

```yml
s3_west_coast:
  service: S3
  access_key_id: ""
  secret_access_key: ""
  ...

s3_east_coast:
  service: S3
  access_key_id: ""
  secret_access_key: ""
  ...

production:
  service: Mirror
  primary: s3_east_coast
  mirrors:
    - s3_west_coast
```

Dosyalar primary servisden sunulur.

### 3.2 ActiveStorage::Blob

**Blob**; dosya adı,  content-type gibi metadataların tutulduğu yerdir. Dosyanın aslı depolama servisinde ya da diskte saklanır. Her blob’un depolama servisinde identifier key'i vardır.

```ruby
create_table :active_storage_blobs do |t|
  t.string   :key,        null: false
  t.string   :filename,   null: false
  t.string   :content_type
  t.text     :metadata
  t.bigint   :byte_size,  null: false
  t.string   :checksum,   null: false
  t.datetime :created_at, null: false

  t.index [ :key ], unique: true
end
```

### 3.3  ActiveStorage::Attachment

**Attachment**, blob ile model arasında bağlantıyı sağlar. Bir blob birden fazla modelle bağlantılı olabilir.

```ruby
create_table :active_storage_attachments do |t|
  t.string     :name,     null: false
  t.references :record,   null: false, polymorphic: true, index: false
  t.references :blob,     null: false
  t.datetime :created_at, null: false

  t.index [ :record_type, :record_id, :name, :blob_id ], name: "index_active_storage_attachments_uniqueness", unique: true
end
```

## 4. Nasıl dosya eklenir?

`Car` adında bir modelimiz olsun ve araba resimlerini tutmak için dosya upload etmek isteyelim. O halde Rails'de yapacağımız ilk iş bir migration ile modelimize resimleri tutacağımız yeni bir kolon eklemektir. Bu dosya upload yapmak istediğimiz her model için geçerlidir.

ActiveStorage ise diğer dosya upload çözümlerinden biraz farklı çalışır. ActiveStorage'da modelinize yeni bir alan eklemenize gerek yok. Dosya ile ilgili bütün bilgiler activestorage install edilirken oluşturulan `blobs` ve `attachments` tablolarında tutulmaktadır.

### 4.1 Bir dosya attach etmek için yapılması gerekenler

```ruby
class Car < ApplicationRecord
  has_one_attached :image
end

# strong_parameters
params.require(:car).permit(:name, :image)
```

### 4.2 Birden fazla dosya attach etmek için yapılması gerekenler

```ruby
class Car < ApplicationRecord
  has_many_attached :images
end

# strong_parameters
params.require(:car).permit(:name, images: [])
```

## 5. Transformation İşlemleri

Transforming işlemcilerin(processor) destekleği metodlar ölçüsünde bloblar üzerinde resimlerin varyasyonlarını oluşturabiliriz. Default processor olarak MiniMagick kullanılmaktadır ancak [Vips](https://www.rubydoc.info/gems/ruby-vips/Vips/Image)'de kullanılabilir.

Biçimlendirmeyi etkinleştirmek için [image_processing](https://github.com/janko-m/image_processing) gemi kullanılmaktadır. Bununla ilgili [pull request](https://github.com/rails/rails/pull/32471).

```bash
gem 'image_processing', '~> 1.2'
```

Aşağıdaki satır yorumlandığı zaman bir defaya mahsus orijinal blob'un biçimlendirilmiş halinin URL'i oluşturulur ve dosyalarınız nerede tutuluyorsa disk/cloud oraya kaydedilir.

```erb
<%= image_tag @cat.image.variant(resize: "100x100") %>
```

## 6. Direk Upload

ActiveStorage bir ruby gemi olmasının yanında bünyesinde javascript kütüphanesi de barındırır. Dosyalarımızı Rails uygulamasına değil de direk cloud bir servise upload etmek istediğimiz zaman bu javascript kodları devreye girmektedir.

Blob oluşurken bir identifier key üretilir ve dosya bu key ile cloud servise upload edilir. Resme tekrar ulaşmak istediğimizde bu key ile bağlantı kurulup erişilmektedir. Direk upload işleminde blob oluşturulma ve resmin store edilme sıralamasında değişiklik olur.

### Direk upload işlemi için

```erb
<%= form.file_field :images, multiple: true, direct_upload: true %>
```

## 7. Tartışma

Rails dosya upload işlemleri için [carrierview](https://github.com/carrierwaveuploader/carrierwave), [paperclip](https://github.com/thoughtbot/paperclipcarrierview
), [refine](https://github.com/refile/refile) gibi bir çok güzel kütüphaneye sahip. Hepsinin kendi içinde farklı yaklaşımları var. Doğru bir tercih için ihtiyaçlar belirlenmeli ve kütüphanelerin özellik setine bakılarak bir seçim yapılmalıdır.

Paperclip geliştiricileri artık Rails'in kendi upload çözümü olan ActiveStorage'ın kullanımını tavsiye ediyor. Paperclip ile ActiveStorage'ı karşılaştıracak olursak dosya validasyonları konusunda ActiveStorage'ın şu an için güzel bir çözümü yok. Bununla ilgili [file_validators](https://github.com/musaffa/file_validators) gibi 3rd party çözüm önerileri var ya da custom validasyon yazılması gerekiyor.

"ActiveStrorage'ı kullanalım mı?, ActiveStorage'a geçiş yapalım mı?" tarzında sorulara araştırmalarım sonucunda şöyle bir cevap elde ettim diyebilirim:

> Uygulamanızda halihazırda kullandığınız bir upload çözümü var ve siz bundan memnunsanız ActiveStorage'a geçiş yapmanız için değerli bir sebebiniz yok demektir. Böyle bir durumda, ActiveStrorage'da bulunan bir özelliği kullanmak istiyorsanız geçiş yapabilirsiniz. Mirror servis özelliği gibi. Yani  başka bir upload çözümüne geçiş yapmayı düşünüyorsanız bunu kullanmak istediğiniz özellik bazında karar verin.
>
> Eğer yeni bir uygulamaya başlıyorsanız ActiveStorage'ı deneyin.

## 8. Kaynakça

- http://guides.rubyonrails.org/active_storage_overview.html
- https://prograils.com/posts/rails-5-2-active-storage-new-approach-to-file-uploads
- https://skillsmatter.com/skillscasts/11458-introduction-to-active-storage
- [RailsConf 2018: Inside Active Storage](https://www.youtube.com/watch?v=-_w4uqoVSpw)
- https://www.engineyard.com/blog/active-storage
- http://www.akitaonrails.com/2017/07/07/upcoming-built-in-upload-solution-for-rails-5-2-activestorage
- http://api.rubyonrails.org/v5.2.0/
