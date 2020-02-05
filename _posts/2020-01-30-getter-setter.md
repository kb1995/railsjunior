---
layout: blog
slug: getter-setter
title: What are Getter and Setter methods in Ruby
subtitle: If you want to either retrieve or set a value of an instance variable from a Ruby class, you need to learn about getters and setters. Learning about attr_accessor is also really useful to help you keep your class clean and tidy.
date: 2020-01-31T7:00:00.000+00:00
category: ruby
---

If you want to either retrieve or set a value of an instance variable from a Ruby class, you need to learn about getters and setters. Learning about attr_accessor is also really useful to help you keep your class clean and tidy.

If you want to learn about Ruby classes, you can read about the basics in <a href="{% post_url 2020-01-27-intro-to-ruby-class %}">this article</a>.

## What is a getter method?

Getter methods help you retrieve the value of an instance variable. If you don't have a getter method, trying to retrieve a variable is going to result in an error.

```ruby
class Company
  def initialize(name)
    @name = name
  end
end

company = Company.new("The Bluth Company")

p company.name
# => undefined method `name' for #<Company:0x00007fe25f021b30> (NoMethodError)
```

Let's include a getter method

```ruby
class Company
  # ...

  # Getter
  def name
    @name
  end
end

company = Company.new("The Bluth Company")
p company.name
# => "The Bluth Company"
```

## What is a setter method?

Setter methods help us assign new values to our instance variables. Let see what happens if you don't have a setter method and you try to assign a new value to your instance variable

```ruby
class Company
  def initialize(name)
    @name = name
  end

  # Getter
  def name
    @name
  end
end

company = Company.new("The Bluth Company")
p company.name
# => "The Bluth Company"

company.name = "Dunder Mifflin"
# => undefined method `name=' for #<Company:0x00007fdd680f50f8 @name="The Bluth Company"> (NoMethodError)
```

So let's create a setter method

```ruby
class Company
  # ...

  # Setter
  def name=(name)
    @name = name
  end
end

# ...
company.name = "Dunder Mifflin"
# => "Dunder Mifflin"
```

## What is a Ruby attribute accessor?

You can see how classes can become quite big with having to define 2 methods for every instance variable. That's why we have access to 3 build-in Ruby methods:

- attr_reader - generates a getter method
- attr_writer - generates a setter method
- attr_accessor - generates both getter and setter methods


```ruby
class Company
  attr_reader :name
  attr_writer :name
  
  def initialize(name)
    @name = name
  end
end

company = Company.new("The Bluth Company")
p company.name
# => "The Bluth Company"

company.name = "Dunder Mifflin"
p company.name
# =>"Dunder Mifflin"
```

When you want both <mark>attr_reader</mark> and <mark>attr_writer</mark>, just use

```ruby
  attr_accessor :name
```

You can define multiple attributes on the same line, which will generate the relevant methods

```ruby
  attr_accessor :name, :email, :address
```

By the way, you can get a list of your instance variables using the built-in <mark>#instance_variables</mark> method and a list of your class methods using <mark>#public_methods</mark> method

```ruby
class User
  attr_accessor :name
  attr_accessor :age
  
  def initialize(name, age)
    @name = name
    @age = age
  end
end

user = User.new("Kris", 24)

p user.instance_variables
# => [:@name, :@age]

p user.public_methods
# => [:age, :age=, :name, :name=]
```

## Combination of attribute accessor

Sometimes you might want to combine your attribute accessor methods depending on your variables. For example, you might want to read and write the <mark>email</mark> variable but only read the <mark>id</mark> variable.

```ruby
attr_accessor :name
attr_reader :id
```

## Using attr_accessor in a Rails model

At its core, a Rails model is just a Ruby class. The model attributes are defined using <mark>attr_accessor</mark> under the hood, but Rails automatically does it for us in order to keep our models tidy.

However, if you want to add an attribute that is not defined in the model's table, you can define it manually using <mark>attr_accessor</mark>. This will create a non-persistent instance variable.

```ruby
# models/customer.rb
class Customer
  # ...

  attr_accessor :password_token
end
```

These non-persistent instance variables are useful in cases when you only need the variable for a limited time or you don't need to store it in your database.

## Further Reading

[https://stackoverflow.com/questions/8774837/what-would-a-default-getter-and-setter-look-like-in-rails](https://stackoverflow.com/questions/8774837/what-would-a-default-getter-and-setter-look-like-in-rails)

[https://dev.to/k_penguin_sato/ruby-getters-and-setters-1p30](https://dev.to/k_penguin_sato/ruby-getters-and-setters-1p30)

[https://www.geeksforgeeks.org/ruby-getters-and-setters-method/](https://www.geeksforgeeks.org/ruby-getters-and-setters-method/)

[https://medium.com/@rondwalker22/how-getter-setter-methods-work-in-ruby-c5f5da07f99](https://medium.com/@rondwalker22/how-getter-setter-methods-work-in-ruby-c5f5da07f99)

[https://stackoverflow.com/questions/47647897/usage-of-attr-accessor-in-a-rails-model-class](https://stackoverflow.com/questions/47647897/usage-of-attr-accessor-in-a-rails-model-class)

[https://ruby-doc.org/docs/ruby-doc-bundle/UsersGuide/rg/accessors.html](https://ruby-doc.org/docs/ruby-doc-bundle/UsersGuide/rg/accessors.html)

[https://medium.com/@amliving/diving-into-rubys-attr-accessor-d34e2ccc5477](https://medium.com/@amliving/diving-into-rubys-attr-accessor-d34e2ccc5477)