---
layout: blog
slug: sorcery-passwordless-authentication
title: Implement a passwordless authentication with Sorcery
date: 2019-07-07T22:00:00.000+00:00
category: rails

---
## What is passwordless authentication?

Passwordless authentication is a verification process that determines whether someone is, in fact, who they say they are without requiring that person to enter their username and password. 

Instead you use a security token to authenticate the user.

Here are some benefits of passwordless auth:

* improved UX - the user doesn't need to remember passwords and request new password if they forget their old one 
* increased security - reduced chance of attacks, password re-use and leaks 
* relatively simple to create - you don't need to create a lot of views

## What is Sorcery?

Even though that I am a big fan of Devise, I started to like how simple and clean Sorcery is. It doesn't take much code to get Sorcery up and running, especially if you are implementing passwordless authentication.

## Authentication through email

This is the basic premise of using email to authenticate the user

* a user inputs their email on an input link
* we send an email with a magic link (one time, randomly generated code)
* The user clicks the link and the service being used will identify the token and exchange it for a live token, logging the user in.

## Goal: create a passwordless auth for a User and send them an email containing a magic link

### Create a user model and controller
```console
rails g controller users index edit update
rails g model User name:string email:string login_token:string login_token_valid_until:datetime
rake db:migrate
```

### Create the skeleton for the login logic
```console
    rails g controller logins create
    rails g controller sessions create destroy
```

### Configure Sorcery

Let's go to [Github](https://github.com/Sorcery/sorcery) and see what we need to configure Sorcery and link it to the User model

In the Gemfile
```rb
    gem 'sorcery'
```

and the in the terminal
```console
    bundle
```
### Create a form that submits an email

Install simple form

In the Gemfile
```rb
    gem 'simple_form'
```

and the in the terminal
```console
    bundle
```

* Create a User model
* Install Sorcery
* Configure Sorcery for the User model
* Disable Password for the User model
* create fields for passwordless auth
* create a sessions controller
  * create
  * destroy
* create a view with an email where a user can submit their email
* create a mailer
* create a magic link
* Send the mailer whenever you submit the form with the email

```