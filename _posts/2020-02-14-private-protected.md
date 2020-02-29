---
layout: blog
slug: public-private
title: The difference between Ruby Public, Private & Protected methods
subtitle: Learn the basics of Ruby access control for methods. Ruby allows you to control the visibility of your methods using three options - public, private and protected.
date: 2020-02-14T7:00:00.000+00:00
category: ruby
---

Ruby, like many other languages, allows you to control the access control of your methods. They can either be public (you can call the from anywhere), private or protected. In Ruby, all methods are public by default, so it's up to you to make them private or protected.

An interesting fact is that private and protected methods are not that widely used in Ruby. Out of almost 200 000 lines of code that represents the Ruby standard library, private appears in the codebase about 1000 times while protected - only about 50 times.

## Public Methods

As I mentioned, the default is public visibility. If a method is public, you can call it inside the class as well from that class' object.

## Private Methods

You can make methods private by adding <mark>private</mark> before the method definition

```ruby
require 'date'
class User
  def welcome_message
    "Happy, #{day_of_week}"
  end

  private
  
  def day_of_week
    Date.today.strftime("%A")
  end
end
```

or making them private after the method definition

```ruby
require 'date'
class User
  def welcome_message
    "Happy, #{day_of_week}"
  end
  
  def day_of_week
    Date.today.strftime("%A")
  end

  private :day_of_week
end
```

In general, the first method is accepted as the preferred syntax within the Ruby community.

The rule for private methods is that you can't call them with an explicit object reference. This ensures that you can only call private methods from within the class that you defined them.

So in our example above, we can call <mark>day_of_week</mark> within the class and it works great, but if we try to call it using our object reference, it will throw an exception.

```ruby
user = User.new

p user.welcome_message
# => "Happy, Friday"
p user.day_of_week
# => private method `day_of_week' called for #<User:0x00007fbb710e0518> (NoMethodError)
```

A note should be made that you can call private methods defined in a superclass within a subclass. You don't need an explicit object reference to call a superclass method from a subclass - this also applies for private methods as well.

```ruby
class Employee < User
  def weekend?
    day_of_week == "Saturday" || day_of_week == "Sunday" ? "It's the weekend!" : "Working today"
  end
end

employee = Employee.new
p employee.weekend?
# => "Working today"
```

## Protected Methods

In a lot of ways, protected methods are similar to private methods. You define protected methods using the same same as private methods. Just like private methods, protected methods can't be called outside the class but they can be called inside the class.

The definition of protected methods is a bit more complicated compared to private methods. You can call protected methods with an explicit receiver (unlike private), but that receiver should always be <mark>self</mark> or an object that inherits from <mark>self</mark>.

```ruby
require 'date'

class User
  def welcome_message
    "Happy, #{day_of_week}"
  end

  protected

  def day_of_week
    Date.today.strftime("%A")
  end
end

class Employee < User
  def weekend?
    day_of_week == "Saturday" || day_of_week == "Sunday" ? "It's the weekend!" : "Working today"
  end

  def self_weekend?
    self.day_of_week == "Saturday" || self.day_of_week == "Sunday" ? "It's the weekend!" : "Working today"
  end
end

p Employee.new.weekend?
# => "Working today"
p Employee.new.self_weekend?
# => "Working today"
```

If you change <mark>#day_of_week</mark> to be private instead of protected, you will get

```ruby
p Employee.new.weekend?
# => "Working today"
p Employee.new.self_weekend?
# => private method `day_of_week' called for #<Employee:0x00007ff70f866f38> (NoMethodError)
```

Therefore, if you need to support explicit receivers, use <mark>protected</mark>.

## Rules can be broken

When coding with Ruby, you are in charge of your code. If you want to call a private method outide the class, Ruby will allow you using the <a href="https://apidock.com/ruby/Object/send" target="_blank">send</a> method.

```
user = User.new
p user.send(:day_of_week)
# => "Friday"
```

Ruby gives you the freedom to change the rules if you want to - the language trusts you that you will make the right choices for your code.

## Further reading

[https://naturaily.com/blog/public-private-protected-ruby](https://naturaily.com/blog/public-private-protected-ruby)

[https://medium.com/@tjoye20/ruby-access-control-basics-public-vs-private-vs-protected-methods-7788b26e04a7](https://medium.com/@tjoye20/ruby-access-control-basics-public-vs-private-vs-protected-methods-7788b26e04a7)

[https://www.codementor.io/@anuraag.jpr/the-difference-between-public-private-and-protected-methods-in-ruby-6zsvkeeqr](https://www.codementor.io/@anuraag.jpr/the-difference-between-public-private-and-protected-methods-in-ruby-6zsvkeeqr)