---
layout: post
read_time: true
show_date: true
title: attr_accessor Usage in Ruby
date: 2015-07-03 14:45:44 +0200
description:
img: banners/brain.jpg
tags: [coding, ruby]
author: Ecmel Albayrak
---

Let's assume we have a class called Person:

```ruby
class Person
end

person = Person.new 
person.name #=> NoMethodError: undefined method `name' for #<Person:0x000055659b8bfb68>
```

If we don't define a method called `name`, we will get an error like above (`NoMethodError`). So let's define our method.

```ruby
class Person
  def name
    @name # instance variable
  end
end

person = Person.new
person.name # => nil
person.name = "Ecmel" # => NoMethodError: undefined method `name='
```

We defined the `name` method, but we can only read it, we cannot assign any value. Let's define writer method.

```ruby
class Person
  def name # reader
    @name
  end

  def name=(str) # writer
    @name = str
  end
end

person = Person.new
person.name = "Ecmel"
person.name # => "Ecmel"
```

Now we can access and change the value of the instance variable (`@name`) using the reader and writer methods. So will we define these methods this way every time? Nope! We can use **attr_reader** for reader methods and **attr_writer** for writer methods.

```ruby
class Person
  attr_reader :name
  attr_writer :name
end
```

We can also use **attr_accessor** for reading and writing methods instead of defining them separately. Moreover `attr_accessor` creates instance variable that called `@name`.

```ruby
class Person
  attr_accessor :name
end

def say_hello
"Hello #{@name}"
end

person = Person.new
person.name = "Ecmel"
person.say_hello # => "Hello Ecmel"
```

![out](http://res.cloudinary.com/dro8cemyf/image/upload/c_scale,h_280,w_346/v1457011914/thatsit.png_m778zf.jpg)
