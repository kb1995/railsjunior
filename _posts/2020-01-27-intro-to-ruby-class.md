---
layout: blog
slug: ruby-class
title: Intro to Ruby Class 101
subtitle: Classes are the basic building block in Object Oriented Programming (OOP). They define the blueprint for creating objects. In this lesson, we will learn everything that we need to start working with Ruby Classes.
date: 2020-01-27T7:00:00.000+00:00
category: ruby
---

## What is a Class?

A class is the basic building block in Object Oriented Programming (OOP) (such as Ruby) and is the "blueprint" from which individual objects are created. So for example, Michael Bluth is an instance of the class <mark>User</mark> and Bulgaria is an instance of the class <mark>Country</mark>.

Every User has different characteristics which can help you to distinguish between them - name, email, billing address, age and so on.

Every User can also have different methods (functions) - calculate the date of birth, sign up to the newsletter, etc.

## Defining a Ruby Class

You can define the class User using the following syntax

```ruby
class User
  # class logic goes here
end
```

Notice that classes are defined using the <mark>class</mark> keyword. Also, class names follow the camel case rule - each word should be uppercase and no spaces are allowed.

## Variables in Ruby Class

There are five different types of variables which can be used in a Class:

<ul>
  <li><b>local variables</b> - variables only available in the method that they are defined in. An example of a local variable is <mark>local_variable = 5</mark></li>
  <li><b>instance variables</b> - variables that are available across methods for an instance of a class. An example of an instance variable is <mark>@instance_variable = 5</mark></li>
  <li><b>class variables</b> - variables that are available across different objects. You can think of it as a characteristic of the class. To define a class variable, use <mark>@@</mark>, for example <mark>@@class_variable = 5</mark></li>
  <li><b>global variables</b> - variables available across different classes. Use the <mark>$</mark> symbol, for example <mark>$global_variable = 5</mark></li>
  <li><b>constants</b> - variables that never change. Even though you only need to uppercase the first letter of a constant, the "Ruby way" of defining a constant is to uppercase the entire variable - <mark>CONSTANT_VARIABLE = 5</mark></li>
</ul>

## Creating a Ruby Object from a Class

Objects are instances of a Class. You can use the <mark>new</mark> method to create objects

```ruby
user1 = User.new
# => #<User:0x00007f9b0c8f7718>
user2 = User.new
# => #<User:0x00007f9b0c8f76f0>
```

## The Initialize method

Another thing to remember about classes is that, unlike methods, you do not pass arguments to classes. Instead, you pass arguments to the <mark>.new</mark> which you can then use to create instance variables for that object. 

This is done using the special <mark>initialize</mark> method inside the class. If you are familiar with other OOP languages, you might have come across this method as a "constructor".

```ruby
class User
  def initialize(first_name, last_name, age)
    @first_name = first_name
    @last_name = last_name
    @age = age
  end
end

user = User.new("Tobias", "Funke", 33)
# => #<User:0x00007fb24a835708 @first_name="Tobias", @last_name="Funke", @age=33>
```

A note should be made that if you pass parameters to the <mark>.new</mark> method but don't define <mark>#initialize</mark> within your class, you will get an error.

```ruby
user = User.new("Tobias", "Funke", 33)
# => wrong number of arguments (given 3, expected 0) (ArgumentError)
```

If your class doesn't require arguments, you don't have to define <mark>#initialize</mark>.

## Defining instance methods in a class

You can define custom methods within a class. Within the method, you can use the instance variables that you defined in the initialize method.

```ruby
class User
  # ...
  def greeting
    "Hello, #{@first_name}."
  end
end

p user.greeting
# => "Hello, Tobias."
```

Remember that every object created by User will have access to the <mark>greeting</mark> method.

## Getter/Setter

The instance variables that you defined when you created the object can't be used outside of your class unless you create getter and setter methods or use <mark>attr_accessor</mark>.

```ruby
p user.first_name
# => undefined method `first_name' for #<User:0x00007fccfe8f90d0> (NoMethodError)
```

To be able to read the variables and assign it a new value from outside the class, you can define two methods, a getter and a setter, within your class.

```ruby
class User
  # ...
  def first_name
    @first_name
  end

  def first_name=(name)
    @first_name = name
  end
end

user = User.new("Tobias", "Funke", 33)
p user.first_name
# => "Tobias"
user.first_name = "Lindsay"
p user.first_name
# => "Lindsay"
```

That's great, but it can be a bit time consuming to have to write all of these methods for every instance variable. Fortunately, Ruby has thought of this problem and created the <mark>attr_accessor</mark> method which makes our classes a lot shorter and nicer looking.

```ruby
class User
  attr_accessor :first_name
  # ...
end
```

This method creates both <mark>first_name</mark> and <mark>first_name=</mark> for us. Very nice.

This is just an overview of the getter/setter concept and the attr_accessor method. I wrote a separate <a href="{% post_url 2020-01-30-getter-setter %}">article</a> where I go into more detail on this concept.

## Defining class methods

Class methods provide the functionality to the class itself. We use the <mark>self</mark> keyword to define class methods.

```ruby
class User
  def self.class_method
    "I am a class method."
  end
  # ...
end

p User.class_method
# => "I am a class method."
p user.class_method
# => undefined method `class_method' for #<User:0x00007fa3350deca8> (NoMethodError)
```

You should remember that you cannot call instance methods on the class just like you can't call class methods on the instance.

## Everything in Ruby is an object

Yes, really! Because everything is an object, everything was created from a class. You can see from which class the object is coming from using the <mark>.class</mark> method.

```ruby
p [].class
# Array

p "".class
# String

p user.class
# User
```

If you know from which class your object is coming from, you then know the list of methods that you can apply on this object. You can check the Ruby documentation for a full list of methods for each class!

## Further reading

[https://ruby-doc.com/docs/ProgrammingRuby/html/tut_classes.html](https://ruby-doc.com/docs/ProgrammingRuby/html/tut_classes.html)

[https://ruby-doc.org/core-2.5.3/Class.html](https://ruby-doc.org/core-2.5.3/Class.html)

[https://www.tutorialspoint.com/ruby/ruby_classes.htm](https://www.tutorialspoint.com/ruby/ruby_classes.htm)

[https://www.rubyguides.com/2019/02/ruby-class/](https://www.rubyguides.com/2019/02/ruby-class/)

[https://blog.makersacademy.com/coding101-ruby-classes-explained-b0645a721288](https://blog.makersacademy.com/coding101-ruby-classes-explained-b0645a721288)

[https://launchschool.com/books/oo_ruby/read/the_object_model#whyobjectorientedprogramming](https://launchschool.com/books/oo_ruby/read/the_object_model#whyobjectorientedprogramming)

[http://ruby-for-beginners.rubymonstas.org/writing_classes/definition.html](http://ruby-for-beginners.rubymonstas.org/writing_classes/definition.html)

[https://www.studytonight.com/ruby/class-in-ruby](https://www.studytonight.com/ruby/class-in-ruby)

[https://medium.com/rubyinside/class-methods-in-ruby-a-thorough-review-and-why-i-define-them-using-class-self-af677ede9596](https://medium.com/rubyinside/class-methods-in-ruby-a-thorough-review-and-why-i-define-them-using-class-self-af677ede9596)




