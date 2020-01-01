---
layout: blog
slug: connect-phone-with-rails-server
title: An easy way to connect your Rails server to your phone
subtitle: Test your Rails application on your mobile phone or another laptop when they are connected to the same IP address
date: 2019-12-23T7:00:00.000+00:00
category: devops
---

There are products such as <a href="https://ngrok.com/" target="_blank">ngrok</a> which expose your localhost to the web, so you can access it on another machine, for example another laptop or your phone. However, I often find that this method is an overkill for simple testing.

## Get your IP address

Search for <mark>Network Utility</mark> and make a record of your IP address.

Another way to find your IP address is to click on the wifi icon in the menu bar with the option key pressed down. This will give you access to bunch of information, including your IP address.

## Start your server

Start your server using

```terminal
rails server -b 0.0.0.0
```

This will allow you to access your Rails server from a different machine. You can read more about it <a href="https://guides.rubyonrails.org/4_2_release_notes.html#default-host-for-rails-server" target="_blank">here.</a>

## Access localhost on your phone

Using your IP address, access your Rails server on another machine using

```terminal
http://your.ip:3000/
```









