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

### Update routes file
```rb
Rails.application.routes.draw do
  #
  get 'sessions/create'
  delete 'sessions/destroy'

  #
  get 'logins/new'
  post 'logins/create'
  
  #
  resources :users
  
  #
  root 'users#index'
end
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

Hook Sorcery to the User model

```rb
class User < ApplicationRecord
  authenticates_with_sorcery!
end
```

Update Application Controller to configure current_user and to let know what rails should do if the user is not authenticated

```rb
class ApplicationController < ActionController::Base
  def user_class
    User  
  end

  def not_authenticated
    redirect_to root_path, alert: 'Not authenticated'
  end
end
```

Finally, include 

```rb
before_action :require_login, only: [:edit, :update]
```

in your UsersController to let know for which actions Sorcery should authenticate

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

In logins/new.html.erb

```erb
<%= simple_form_for @user, url: logins_create_path, method: :post do |f| %>
  <%= f.input :email, label: "Email" %>
  <%= f.submit "Get a magic link" %>
<% end %>
```

Your LoginsController should look like

```rb
class LoginsController < ApplicationController
  def new
    @user = User.new
  end

  def create
    user = User.find_or_create_by!(email: params[:user][:email])
    user.update!(login_token: SecureRandom.urlsafe_base64,
      login_token_valid_until: Time.now + 60.minutes)
      
    url = sessions_create_url(login_token: user.login_token)
    LoginMailer.send_email(user, url).deliver_now

    redirect_to root_path, notice: 'Login link sent to your email'
  end
end
```

### Create the LoginMailer

create a file app/mailers/login_mailer and put this info in

```rb
class LoginMailer < ApplicationMailer
  def send_email(user, url)
    @user = user
    @url  = url

    mail to: @user.email, subject: 'Sign-in into someapp.com'
  end
end
```

Now we need to create the view of the letter. Create a new file app/views/login_mailer/send_email.html.erb

```erb
<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <h1>Welcome <%= @user.email %>,</h1>
    <a href="<%= @url %>">Magic link</a>
</html>
```

### (Optional) Configure LetterOpener

TODO

### Sessions controller

```rb
class SessionsController < ApplicationController
  def create
    # If a login token has expired, we don't log in the user
    user = User.where(login_token: params[:login_token])
              .where('login_token_valid_until > ?', Time.now).first

    if user
      # nullify the login token so it can't be used again
      user.update!(login_token: nil, login_token_valid_until: 1.year.ago)

      # sorcery helper
      auto_login(user)

      redirect_to root_path, notice: 'Congrats. You are signed in!'
    else
      redirect_to root_path, alert: 'Invalid or expired login link'
    end
  end

  def destroy
    # sorcery helper
    logout

    redirect_to root_path, notice: 'You are signed out'
  end
end
```

### Final touches - UsersController and user views

Your UsersController should look like this

```rb
class UsersController < ApplicationController
  before_action :require_login, only: [:edit, :update]
  def index
  end

  def edit
  end

  def update
    current_user.update! user_params
    redirect_to root_path
  end

  private 
  def user_params
    params.require(:user).permit(:name)
  end
end
```

update views/users/index.html.erb

```erb
<% if current_user %>
  <h1><%= current_user.name.present? ? "My name is #{current_user.name}" : "Input your name using the link below" %></h1>
  <%= link_to  "Edit current user", edit_user_path(current_user) %> <br>
  <%= link_to  "Sign out", sessions_destroy_url, method: :delete %> <br>
<% else %>
  <h1>Login to input your name</h1>
  <%= link_to  "Login to edit", logins_new_path %>
<% end %>
```

and finally views/users/edit.html.erb

```erb
<%= simple_form_for current_user do |f| %>
  <%= f.input :name, label: "Name" %>
  <%= f.submit "Update name" %>
<% end %>
```

To make sure that the authentication works, go to incognito and copy paste the edit page. You can't access the page unless you are logged in with the correct account.

Here is a link to my Github repo if you want to see the source code of this example (TODO)