---
layout: blog
slug: pass-attr-acessors-json
title: How to pass attr accessors in your JSON response
subtitle: If your attr accessors are not being passed through your json response, here's how to fix it
date: 2020-03-07T7:00:00.000+00:00
category: rails
---

So you have defined an attr accessor in your model which you want to pass down through a json response. But it doesn't work.

First of all, the reason why it is not working. Attr_accessor defines a getter and a setter methods. Therefore, if we want to retrieve it from our json, we need to define it as a method. You can read more about attr_accessors in my <a href="{% post_url 2020-01-30-getter-setter %}">article</a>.

If you have defined an attr_accessor called <mark>distance</mark>, you can pass the getter method using the following syntax.

```ruby
render json: user, methods: [:distance]
```

Now <mark>distance</mark> is in your json. 🎉