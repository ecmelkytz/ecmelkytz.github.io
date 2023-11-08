---
layout: post
read_time: true
show_date: true
title: Ruby - Upcase, Downcase, Capitalize, Titleize Methods (Non-Ascii)
date: 2014-11-17 21:58:03 +0200
description: Single neuron perceptron that classifies elements learning quite quickly.
img: banners/coding.png 
tags: [ruby, coding]
author: Ecmel Albayrak
github:
mathjax: yes
---
If you write the words using the English alphabet, methods such as upcase, downcase, capitalize, titleize will work as you wish. However, when characters from alphabets such as Turkish and Russian are involved, these methods do not yield the desired results. [UnicodeUtils][Unicodeutils] gem can be used for such problems.

## Upcase

This method converts all characters of the entered text to uppercase.

```ruby
> "isim".upcase
=> "ISIM" # didn't work right

> "isim".mb_chars.upcase.to_s
=> "ISIM" # didn't work right

> UnicodeUtils.upcase("isim")
=> "ISIM" # didn't work right

# Solution
> UnicodeUtils.upcase("isim", :tr)
=> "İSİM"
```

## Downcase

This method converts all characters of the entered text to lowercase.

```ruby
> "IŞIK".downcase
=> "işik" # didn't work right

> "IŞIK".mb_chars.downcase.to_s
=> "işik" # didn't work right

> UnicodeUtils.downcase("IŞIK")
=> "işik" # didn't work right

# Solution
> UnicodeUtils.downcase("IŞIK", :tr)
=> "ışık"
```

## Capitalize

This method converts only the first letter of the entered text to uppercase.

```ruby
> "izmir".capitalize
=> "Izmir" # didn't work right

> "izmir".mb_chars.capitalize.to_s
=> "Izmir" # didn't work right

# Solution
> UnicodeUtils.upcase("izmir", :tr).capitalize
=> "İzmir"
```

## Titleize

This method converts the first letter of each word of the entered text to uppercase.

```ruby
> "izmir çok güzel".titleize
=> "Izmir Çok Güzel" # didn't work right

> "izmir çok güzel".mb_chars.titleize.to_s
=> "Izmir Çok Güzel" # didn't work right

# Solution
> UnicodeUtils.titlecase("izmir çok güzel", :tr)
=> "İzmir Çok Güzel"
```

[Unicodeutils]: https://github.com/lang/unicode_utils
