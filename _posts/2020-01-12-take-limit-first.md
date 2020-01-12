---
layout: blog
slug: rails-take-limit-first
title: Rails' first vs take vs limit
subtitle: Learn about the differences between the methods first vs take vs limit
date: 2020-01-12T7:00:00.000+00:00
category: rails
---

There are some notable differences between the #take, #first & #limit methods that we, as Rails developers, should be aware of.

## Take method

The [Take](https://apidock.com/rails/ActiveRecord/FinderMethods/take) method returns a record without any implied order. You can also specify a parameter, for example, #take(5) which will return the number of records you have specified in your parameter.

#take generates the following SQL

```terminal
004 > User.take
User Load (0.5ms)  SELECT "users".* FROM "users" LIMIT $1  [["LIMIT", 1]]

005 > User.take(5)
  User Load (0.5ms)  SELECT "users".* FROM "users" LIMIT $1  [["LIMIT", 5]]
```

The order is indeed at random

```terminal
006 > User.take(5).map(&:id)
  User Load (0.6ms)  SELECT "users".* FROM "users" LIMIT $1  [["LIMIT", 5]]
 => [2, 108, 37, 487, 20] 
```

If you don't care about the order or if you want an arbitrary record, #take is a good way to go.

<hr>

Notice that #take is also an [array method](https://apidock.com/ruby/Array/take) which works in a slightly different way. If you call Array#take, a parameter is required and it returns the first N records in the form of an array. 

Therefore, <mark>User.take(5).take(1)</mark> is a valid syntax but <mark>User.take(5).take</mark> will throws an error.

## First method

The [First](https://apidock.com/rails/ActiveRecord/FinderMethods/first) method is similar to #take, but it returns the first record (or N records if a parameter is specified).

#first generates the SQL

```terminal
007 > User.first
  User Load (10.6ms)  SELECT "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT $1  [["LIMIT", 1]]
008 > User.first(5)
  User Load (0.6ms)  SELECT "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT $1  [["LIMIT", 5]]
```

Notice that the #first adds an additional ordering query to the SQL command. This means that in general #take will be faster than #first since #take can stop as soon as it finds the first row. The difference often might be negligible, but if you don't need the first record just, I recomment you to just use #take.

<hr>

Just like #take, #first also has its [array version](https://apidock.com/ruby/Array/first). It works very similarly its Rails version and as well as Array#take.

Array#first doesn't require an argument, unlike Array#take. You should also note that Array#first(1) will return an array unlike Array#first, which will return a single value.

Another difference can be found when you call the methods on empty arrays

```terminal
039 > [].take(1)
 => [] 
040 > [].first
 => nil 
```

You should decide which behaviour you want in a given scenario within your app.

## Limit method

The [Limit](https://apidock.com/rails/ActiveRecord/QueryMethods/limit) method specifies a limit for the number of records to retrieve. Unlike the previous two methods, #limit requires a parameter. It always returns an Active Record Relation.

#limit generates the SQL

```terminal
028 > User.limit(5)
  User Load (0.5ms)  SELECT "users".* FROM "users" LIMIT $1  [["LIMIT", 5]]
029 > User.limit(5).class
 => User::ActiveRecord_Relation 
```

Since #limit returns an Active Record Relation, this means that you can continue scoping afterwards

```terminal
041 > User.limit(5).where(id: 1)
  User Load (0.4ms)  SELECT "users".* FROM "users" WHERE "users"."id" = $1 LIMIT $2  [["id", 1], ["LIMIT", 5]]
```

This won't work with either #take or #first because these methods return an array. Therefore, an error will be thrown at you.

<hr>

Limit **does not** have a corresponding Array method, so if you call it on an Array, it will throw an error.







