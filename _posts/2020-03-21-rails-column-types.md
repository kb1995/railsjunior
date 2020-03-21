---
layout: blog
slug: rails-column-types
title: What are the Ruby on Rails column types?
subtitle: Find out all the different column types that you can add in your Ruby on Rails app
date: 2020-03-21T7:00:00.000+00:00
category: rails
---

According to Rails source code, these are the native database types

```ruby
NATIVE_DATABASE_TYPES = {
  primary_key: "bigint auto_increment PRIMARY KEY",
  string:      { name: "varchar", limit: 255 },
  text:        { name: "text" },
  integer:     { name: "int", limit: 4 },
  float:       { name: "float", limit: 24 },
  decimal:     { name: "decimal" },
  datetime:    { name: "datetime" },
  timestamp:   { name: "timestamp" },
  time:        { name: "time" },
  date:        { name: "date" },
  binary:      { name: "blob" },
  blob:        { name: "blob" },
  boolean:     { name: "tinyint", limit: 1 },
  json:        { name: "json" },
}
```

Let's add a bit more detail.

<ul>
  <li><mark>:primary_key</mark> - translates into whatever the primary key datatype your database of choice requires</li>
  <li><mark>:string</mark> - use for short strings such as names and emails</li>
  <li><mark>:text</mark> - use for long strings, normally captured by textarea</li>
  <li><mark>:integer</mark> - it's a whole number, eg. 0,12,12532</li>
  <li><mark>:float</mark> - decimal numbers stored with fixed floating point precision</li>
  <li><mark>:decimal</mark> - stored precision varies; good for exact math calculations</li>
  <li><mark>:datetime</mark> - both date and time</li>
  <li><mark>:timestamp</mark> - same as datetime; usually used for created_at/updated_at</li>
  <li><mark>:time</mark> - hours, minutes, seconds</li>
  <li><mark>:date</mark> - year, month, day</li>
  <li><mark>:binary</mark> - store images, videos and other files in their original format in chunks of data called blobs</li>
  <li><mark>:blob</mark> - similar to binary, contains the metadata about a file. Blobs are intended to be immutable</li>
  <li><mark>:boolean</mark> - true/false</li>
  <li><mark>:json</mark> - from Rails 5, you can natively store json values thanks to this <a href="https://github.com/rails/rails/pull/21110" target="_blank">Pull Request</a>. A lot of people still use Postgres' jsonb datatype.</li>
</ul>

## Array columns (if using Postgres)

Array is not a native database type in Rails, but if you want to add an array as a column, you can do the following migration

```ruby
def change
  add_column :users, :locations, :text, array: true, default: []
end
```

This uses the Postgres datatype.

## Other Postgres datatypes you can use

Besides arrays, you can add other Postgres datatypes, such as jsonb, in your Rails app. Rails has an entire <a href="https://guides.rubyonrails.org/active_record_postgresql.html#enumerated-types" target="_blank">article</a> which covers this topic.


## List the types of columns in your model

I came across <a href="https://medium.com/chaos-elevators/rails-how-to-get-column-types-in-activerecord-ff2c6665a248" target="_blank">this article</a> which introduced me to <mark>#columns_hash</mark>. This method loads the schema and returns useful information about your model columns

```console
> User.columns_hash["email"]

=> #<ActiveRecord::ConnectionAdapters::PostgreSQL::Column:0x00007fa7b42b1840 @name="email", @sql_type_metadata=#<ActiveRecord::ConnectionAdapters::SqlTypeMetadata:0x00007fa7b42b1958 @sql_type="character varying", @type=:string, @limit=nil, @precision=nil, @scale=nil>, @null=true, @default=nil, @default_function=nil, @collation=nil, @comment=nil, @serial=nil
```

We can loop through them to get a list for our model

```console
> User.columns_hash.each{|k,v| puts "#{k} is #{v.type} type"}

=> id is integer type
=> email is string type
=> encrypted_password is string type
...
```