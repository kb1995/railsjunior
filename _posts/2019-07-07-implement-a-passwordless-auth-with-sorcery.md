---
layout: blog
slug: sorcery-passwordless-authentication
title: Implement a passwordless authentication with Sorcery
date: 2019-07-07T22:00:00.000+00:00
category: rails

---
## What is passwordless authentication?

## What is Sorcery?

Even though that I am a big fan of Devise, I started to like how simple and clean Sorcery is. It doesn't take much code to get Sorcery up and running, especially if you are implementing passwordless authentication.

## Goal of this article - create a passwordless auth for a User and send them an email containing a magic link

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

    user.update!(login_token: SecureRandom.urlsafe_base64, login_token_valid_until: Time.now + 60.minutes)