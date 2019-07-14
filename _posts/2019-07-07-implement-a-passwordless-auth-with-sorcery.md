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

      user.update!(login_token: SecureRandom.urlsafe_base64, login_token_valid_until: Time.now + 60.minutes)erge
      

      ```HTML
      <h2 id="where-to-start">Where to start</h2>
      
      <p>First, you are going to want to learn front-end programming - this is what you actually see. Back-end programming is all that confusing stuff that goes on the actual server like databases and request handling. It is possible to make an entire website without coding anything on the back-end, so you should definitely start with front-end programming.</p>
      
      <p>There are three main languages that are part of front-end programming: <em>HTML</em>, <em>CSS</em>, and <em>JavaScript</em>. Let’s have a look at what each of them is:</p>
      ```
      {: .wrap}
      
      As you can see, it has some normal text but then also some other bits of code. This is called markup - hence the name Hypertext Markup Language.
      
      ### CSS
      CSS stands for Cascading Style Sheet and controls what a webpage looks like. Without CSS, the entire web would look something like this:
      ![This article without CSS][no-css]
      Here is a snippet of CSS:
      ```CSS
      #where-to-start {
          color: red;
          text-align: center;
          border: 5px solid red;
      }
      ```
      
      ### JavaScript
      JavaScript is what makes web pages do stuff. It has stuff like if/else statements and functions (which you will learn about later). Here is a snippet of JavaScript:
      
      ```JavaScript
      var x = 5;
      
      if(x < 10) {
          console.log("x is a one-digit number.");
      } else {
          console.log("x has more that one digit.");
      }
      ```