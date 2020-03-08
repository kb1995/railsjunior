---
layout: blog
slug: run-worker-from-console
title: How to run a worker from the console
subtitle: If you want to test your sidekiq worker from the console, you can call it using the following command
date: 2020-03-08T7:00:00.000+00:00
category: rails
---

Sometimes you just want to test your worker in the console immediately. You can do so by defining a new instance of your worker class and pass any params that you might have

```terminal
:001 > MyWorker.new.perform(any_params)
```

This of course shouldn't be done in your code because sidekiq is meant to be performed async.
