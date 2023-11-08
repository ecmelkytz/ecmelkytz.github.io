---
layout: post
read_time: true
show_date: true
title: "Rails: ActiveRecord Enum Usage"
date: 2016-03-10 14:00:01 +0200
img: banners/network.jpg
tags: [coding, rails, ruby]
category:
author: Ecmel Albayrak
---
Let's have a model called Car and we want to keep information such as whether the car is rented, available or unavailable.

##### Should we use more than one column? Why?

Let's imagine that we added these fields to our table as follows.

```ruby
class AddStatusesToCars < ActiveRecord::Migration
  def change
    add_column :cars, :rented, :boolean
    add_column :cars, :available, :boolean
    add_column :cars, :unavailable, :boolean
  end
end
```

According to this scenario, the car must be either rented, available or unavailable. It should not be both rented and available at the same time. We can handle this with validation. However, this time, when one is true, the other two columns will be empty. So let's look at a way to do this in a single column:

```ruby
class AddStatusToCars < ActiveRecord::Migration
  def change
    add_column :cars, :status, :integer, default: 0
  end
end
```

```ruby
class Car < ActiveRecord::Base
  STATUSES = %w{rented available unavailable}

    def status_to_s
      STATUSES[status]
    end

    def available?
      status_to_s == 'available'
    end

    def rented?
      status_to_s == 'rented'
    end

    def unavailable?
      status_to_s == 'unavailable'
    end
end
```

Let's look at its usage:

```ruby
> Car.first.status_to_s
# => “available”

> Car.first.available?
# => true

> Car.first.rented?
# => false

> Car.first.unavailable?
# => false
```

This may be preferable instead of creating three columns. But, how do we solve the process of creating extra methods?

Let's call the Enum into play.

Add the status field to our car model:

```ruby
class AddStatusToCars < ActiveRecord::Migration
  def change
    add_column :cars, :status, :integer, default: 0
  end
end
```

Define our statuses with enum in our car model.

```ruby
class Book < ActiveRecord::Base
  enum status: [:available, :rented, :unavailable]
  # or define with number
  # enum status: [
  #   available:   1,
  #   rented:      2,
  #   unavailable: 3
  # ]
end
```

Let's look at its usage:

```ruby
> car = Car.first
> car.status
# => “available”

> car.rented?
# => false

# Change the car status to rented
car.status = :rented
car.rented! # updated
car.rented?
# => true

# To find all rented cars (scope method)
 > Car.rented
# => SELECT "cars".* FROM "cars" WHERE "cars"."status" = ?  [["status", 1]]
# [#<Car:0x007f41d3eeeeee
# id: 1,
# status: 1,
# ]


# To find all unavailable cars (scope method)
> Car.unavailable
# => SELECT "cars".* FROM "cars" WHERE "cars"."status" = ?  [["status", 2]]
# => []  (There isn't any available car)
```
