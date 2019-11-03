---
layout: blog
slug: symbols-vs-string
title: Symbols vs Strings
subtitle: Learn about Ruby's symbols and strings - what are the similarities and differences and which of the two is faster
date: 2019-07-27T6:00:00.000+00:00
category: ruby

---

## What are Symbols?

A lot of programming languages don't have Symbols - they only have Strings. In those languages, Strings are used as identifiers as well as storing data. 

<b>In Ruby</b>, this logic is split into two separate types - we use Symbols as identifiers and Strings are used for working with data.

If you have ever worked with Ruby or Ruby on rails, you have encountered Symbols. They look like this

```rb
:post
:index
:user
```

For whatever reason, you might be interested to see which Symbols are currently in the memory of your application using the command

```console
Symbol.all_symbols
```

<hr>

You should be more interested in understanding what Symbols are, not when to use them. Generally, if you understand what they are, chances are that you will be fine.

Use Symbols when you are sure that the value will remain constant. For example, hash keys.

Use Strings when you want to change the value or you want to use a string method on it.

## Where are Symbols used

Symbols can be used for hash keys

```rb
hash = {
  :name => "Rails Junior", 
  :url => "https://railsjunior.com/", 
  :monthly_readers => 10000
  }
```

or using the newer Ruby syntax:

```rb
hash = {
  name: "Rails Junior", 
  url: "https://railsjunior.com/", 
  monthly_readers: 10000
  }
```

Another common usage of Symbols is for defining the <mark>attr_accessor</mark> of a class

```rb
class User  
attr_accessor :first_name  
end

new_user = User.new
new_user.name = "Gob"
```

In Rails, we might use Symbols to define what to render - for example partials or actions.

```rb
respond_to do |format|
  format.js { render :update }
end
```

## Main differences between Strings and Symbols

<b>Symbols are immutable; Strings aren't.</b>

Being immutable means that the values won't change. 

Therefore, Symbols are faster than Strings. In fact, Strings are about 1.7 times slower than Symbols!

To delve deeper, let's open the rails console and define a string and assign it to a variable. 

This will create a new object in memory. 

If we repeat the same assignment, it will assign the same definition to another object in memory.

```console
foo = "hi"
=> "hi" 

foo.object_id
=> 70346754598460 

foo = "hi"
=> "hi" 

foo.object_id
=> 70346736294700 
```

That's two different objects for our String - bad for memory.

Let's repeat the same thing, but use Symbols instead.

```console
foo = :hi
=> :hi 

foo.object_id
=> 5286428 

foo = :hi
=> :hi 

foo.object_id
=> 5286428 
```

Multiple uses of the same Symbol have the same object ID. Therefore, they are the same object! 

On the other hand, Strings will be a different object with unique object ID. Therefore, different objects.

<hr>

Not only that the objects themselves are faster, but also some methods, such as comparing two Symbols, are faster than the equivalent methods for Strings.

Finally, let's be honest - Symbols looks better and are more readable than Strings.

## Convert between Symbols and Strings

Why do you want to convert Symbols to Strings? One of the main reasons is that Symbols only have a subset of the methods that Strings have. For example, Strings have the <mark>.split</mark> method, while Symbols don't.

Check out the Ruby docs for full method list of <a href="https://ruby-doc.org/core-2.5.0/Symbol.html" target="_blanks">Symbol</a> and <a href="https://ruby-doc.org/core-2.4.0/String.html" target="_blanks">String</a>.

<hr>

Converting between symbols to strings is easy - use the <mark>.to_s</mark> method.

```console 
foo = :hi
=> :hi 

foo.class
=> Symbol 

foo = foo.to_s
=> "hi" 

foo.class
=> String 
```

Converting string to symbols is equally easy - use the <mark>.to_sym</mark> method.

```console
foo = "hi"
=> "hi" 

foo.class
=> String 

foo = foo.to_sym
=> :hi 

foo.class
=> Symbol 
```

## Further reading

[https://culttt.com/2015/04/22/what-are-symbols-in-ruby/](https://culttt.com/2015/04/22/what-are-symbols-in-ruby/)

[https://medium.com/@lcriswell/ruby-symbols-vs-strings-248842529fd9](https://medium.com/@lcriswell/ruby-symbols-vs-strings-248842529fd9)

[https://reactive.io/tips/2009/01/11/the-difference-between-ruby-symbols-and-strings](https://reactive.io/tips/2009/01/11/the-difference-between-ruby-symbols-and-strings)

[https://coderwall.com/p/kuhpvg/difference-between-ruby-string-and-symbol](https://coderwall.com/p/kuhpvg/difference-between-ruby-string-and-symbol)

[https://www.rubyguides.com/2018/02/ruby-symbols/](https://www.rubyguides.com/2018/02/ruby-symbols/)