---
layout: blog
slug: sorcery-passwordless-auth
title: Implement a passwordless auth with sorcery
date: 2019-07-07T22:00:00.000+00:00
category: rails

---
Short introduction to passwordless auth

Short introduction to sorcery

Sorcery makes it simple to create a passwordless auth.

Task - create a passwordless auth for the User model and send them an email with a magic link

    user.update!(login_token: SecureRandom.urlsafe_base64, login_token_valid_until: Time.now + 60.minutes)