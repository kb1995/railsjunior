---
layout: blog
slug: safe-navigation-operator-in-ruby
title: The safe navigation operator (&.) in Ruby 
date: 2019-11-02T7:00:00.000+00:00
category: ruby

---

Ruby 2.3.0 introduced the Safe Navigation Operator <mark>&.</mark>. Over the last couple of months, the safe operator has played a big part of the programming flow of my team (mainly due to the CMS integration with the marketing website) and it has saved us many lines of code. Let's explore what it is and what are the pros and cons of using it.

## Comparison to <mark>try!</mark>

A lot of comparison can be drawn from Ruby's <mark>try!</mark> method and the safe operator <mark>&.</mark>

However, there are several differences that you should be aware of.

Method name is syntactically required when using <mark>&.</mark>
```ruby
obj.try! {} # valid
obj&. {}    # syntax error
```

If using <mark>&.</mark>, attribute assignment is valid

```ruby
obj&.attr = "Tobias Funke" # valid
obj.try!(:attr) = "Tobias Funke" # syntax error
```

Generally speaking, the safe operator is better compared to Rails' <mark>try</mark> or Ruby's <mark>try!</mark> (even though that this might be subjective depending on the developer). There are several points for this argument

<ul>
  <li><mark>&.</mark> is shorter and more readable compared to <mark>try</mark></li>
  <li>the safe operator throws an error if nonexistent method is being invoked as opposed to the <mark>try</mark> method</li>
  <li>attribute assignment is valid when using the safe operator</li>
  <li>it's faster</li>
</ul>

## Performance of the safe operator

Regarding my last point, I came across this <a href="https://gist.github.com/hopsoft/ae361319c54bbcb4f8e2" target="_blank">benchmark</a> which compared the safe operator against other common methods for testing for <mark>nil</mark>.

The safe operator had a similar speed compared to a simple check for nil <mark>nil && nil.length</mark> and it was ~4 times faster compared to Rails' <mark>try</mark> method.

## Why do we need it?

In some cases, our object might be nil which might break our code if we call a specific method on that object. We might want to prevent our app from raising an error and the safe operator <mark>&.</mark> will do exactly that - it will return nil after our method call even if our object is nil.

## Example

Let's say that we have a user with a team and we want to get the name of that team. If the user is present, it will work as expected.

```console
2.6.5 :004 > user.team.name
 => "The Bluth Company"
```

But what if the the user is not present?

```console
2.6.5 :008 > user
 => nil 
2.6.5 :009 > user.team
Traceback (most recent call last):
        1: from (irb):9
NoMethodError (undefined method `team' for nil:NilClass)
```

One solution is to create an if statement

```ruby
if user.present?
  teamName = user.team.name
else
  teamName = nil
end
```

Or we can use the safe operator

```terminal
2.6.5 :010 > teamName = user&.team.name
 => nil 
```

Let's expand this problem further. What if the user is present, but it doesn't have a team?

```terminal
2.6.5 :033 > teamName = user&.team.name
Traceback (most recent call last):
        1: from (irb):33
NoMethodError (undefined method `name' for nil:NilClass)
```

We can quickly fix this error using a safe operator.

```terminal
2.6.5 :034 > teamName = user&.team&.name
 => nil 
```

## Why you might not want to use the safe operator

While conducting my research for this article, I found some comments and articles that criticize the safe operator. Even though these comments might have merit, I think it is a great addition to the toolkit of a Rails developer.

I learnt that there are several potential problems with the safe operator that you should be aware of.

<ul>
  <li><b>your code might be harder to read</b> - if a new developer who picks up your code doesn't know what the safe operator is, they might be confused by what <mark>&.</mark> means.</li>
  <li><b>debugging is harder</b> - if you were to implement the example that I used <mark>user&.team&.name</mark>, you might struggle to find where the error lies. It might be that the user is not defined, or maybe the team is not defined or that the team doesn't have a name. If you didn't use <mark>&.</mark> the answer might be a lot clearer.</li>
</ul>

## Further reading

[https://stackoverflow.com/questions/45825363/what-is-the-difference-between-try-and-safe-navigation-operator-in-ruby](https://stackoverflow.com/questions/45825363/what-is-the-difference-between-try-and-safe-navigation-operator-in-ruby)

[https://rubyinrails.com/2017/11/17/safe-navigation-operator-ampersand-dot-in-ruby/](https://rubyinrails.com/2017/11/17/safe-navigation-operator-ampersand-dot-in-ruby/)

[http://mitrev.net/ruby/2015/11/13/the-operator-in-ruby/](http://mitrev.net/ruby/2015/11/13/the-operator-in-ruby/)

[https://www.theguild.nl/5-reasons-not-to-use-safe-navigation-operators/](https://www.theguild.nl/5-reasons-not-to-use-safe-navigation-operators/)

[https://docs.ruby-lang.org/en/2.4.0/NEWS-2_3_0.html](https://docs.ruby-lang.org/en/2.4.0/NEWS-2_3_0.html)

[https://apidock.com/rails/v3.2.1/Object/try](https://apidock.com/rails/v3.2.1/Object/try)

[https://stackoverflow.com/questions/37977721/why-is-safe-navigation-better-than-using-try-in-rails](https://stackoverflow.com/questions/37977721/why-is-safe-navigation-better-than-using-try-in-rails)