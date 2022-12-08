---
layout: "../../layouts/BlogPost.astro"
title: "Elegant Service Objects"
description: "A guide on how to write low friction service objects for a Ruby on Rails application."
pubDate: "Feb 28 2021"
---

Service objects are one of my favourite design patterns. They aren’t glamorous, but can bring a lot of organizational value to a code base. They’re easy to test, and can take a lot of complexity out of your models and controllers.

The basic idea behind a service object is to use the power of objects to encapsulate our business logic. While we could just as easily pull our business logic into a series of class methods, we wouldn’t be able to achieve the same level of encapsulation that can be achieved with an object.

Here’s an example of a basic service object that updates the balance of an order.

```ruby
class UpdateOrderBalance
  def initialize(order)
    @order = order
  end

  def call
    @order.balance = @order.total - payment_total
  end

  private

  def payment_total
    @payment_total ||= @order.payments.sum(&:amount)
  end
end
```

And here it is in action.

```ruby
UpdateOrderBalance.new(order).call
```

## Cleaning up the interface

Great! We've made our first service object. Though calling `new` preceded immediately by `call` isn’t exactly a pattern that exudes elegance. Ultimately we still want to instantiate our service object and then invoke it, but we want to look good doing it.

One way we can accomplish this is by building a new base class, which abstracts away the logic of calling `new` and `call`.

```ruby
class Service
  def self.call(*args)
    new(*args).call
  end
end
```

We can then update our `UpdateOrderBalance` service object to make use of our new base service class.

```ruby
require_relative 'service'

class UpdateOrderBalance < Service
  def initialize(order)
    @order = order
  end

  def call
    @order.balance = @order.total - payment_total
  end

  private

  def payment_total
    @payment_total ||= @order.payments.sum(&:amount)
  end
end
```

The end result is a much cleaner interface for our service objects, as we need only invoke a single method.

```ruby
UpdateOrderBalance.call(order)
```

This is admittedly a minor improvement, but if I’m going to be writing potentially hundreds of service objects, I don’t want to work with a verbose interface. Ruby is a joy to work with, and I want my service objects to incite that same joy.

## Taking it a step further

Because I'm a lazy software developer, I don't want to have to write the name of each argument three times when defining the `initialize` method for new service objects. One option for getting around this repetition is to make use of the [dry-initializer](https://github.com/dry-rb/dry-initializer) gem from the [dry-rb](https://dry-rb.org) collection.

It's a good idea to think twice about adding new dependencies to any project, so consider this an optional improvement, depending on your appetite for additional dependencies.

```ruby
require 'dry-initializer'

class Service
  extend Dry::Initializer

  def self.call(*args)
    new(*args).call
  end
end
```

```ruby
require_relative 'service'

class UpdateOrderBalance < Service
  param :order

  def call
    order.balance = order.total - payment_total
  end

  private

  def payment_total
    @payment_total ||= order.payments.sum(&:amount)
  end
end
```

The external interface of our service object remains unchanged, but we've cut down on some of the boilerplate required. Beyond cleaning things up, [dry-initializer](https://github.com/dry-rb/dry-initializer) offers some additional benefits. If you're interested, I recommend checking out the [dry-initializer docs](https://dry-rb.org/gems/dry-initializer/3.0/).

## Wrapping up

I'm a big fan of service objects. I write a lot of them, and want the process to be as enjoyable as possible. Ease of use is important with any tool; if it's painful to use, people won't reach for it.
