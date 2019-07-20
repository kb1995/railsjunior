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

## Enough theory, let's get to coding
Our goal in this article is to create a passwordless authentication system for the User model and send the user an email containing a magic link. When a user click on the magic link, they are automatically signed in.

## Create a user controller and model
Pay attention to the login_token and the login_token_valid_until - we will use those to store information about the passwordless auth.
```console
rails g controller users index edit update
rails g model User name:string email:string login_token:string login_token_valid_until:datetime
rake db:migrate
```

## Create the skeleton for the authentication logic
In these controllers, we will validate the token and login the user.
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

## Configure Sorcery

Let's go to [Github](https://github.com/Sorcery/sorcery) and see how to configure Sorcery, in order to link it to the User model.

We won't need to use all the steps in the example, because we don't want passwords.

In the Gemfile, include
```rb
gem 'sorcery'
```

and in the terminal execute the command
```console
bundle
```

Next, we hook Sorcery to the User model by simple writing

```rb
class User < ApplicationRecord
  authenticates_with_sorcery!
end
```

Update Application Controller to configure current_user.

We also let rails know what should happen if the user is not authenticated successfully.

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

Finally, include require_login in the UsersController in order to let Sorcery know which actions require authentication.

```rb
class UsersController < ApplicationController
  before_action :require_login, only: [:edit, :update]
  def index
  end

  def edit
  end

  def update
  end
end
```

## Create a form that sends an email

Install simple form

In the Gemfile, add
```rb
gem 'simple_form'
```

and then in the terminal
```console
bundle
```

In logins/new.html.erb, we define a simple form that submits an email address.

```erb
<%= simple_form_for @user, url: logins_create_path, method: :post do |f| %>
  <%= f.input :email, label: "Email" %>
  <%= f.submit "Get a magic link" %>
<% end %>
```

Next, let's update our LoginsController to look like this

```rb
class LoginsController < ApplicationController
  def new
    @user = User.new
  end

  def create
    # the user might already exist in our db or it might be a new user
    user = User.find_or_create_by!(email: params[:user][:email])

    # create a login_token and set it up to expiry in 60 minutes
    user.update!(login_token: SecureRandom.urlsafe_base64,
      login_token_valid_until: Time.now + 60.minutes)
    
    # create the url which is to be included in the email
    url = sessions_create_url(login_token: user.login_token)

    # send the email
    LoginMailer.send_email(user, url).deliver_later

    redirect_to root_path, notice: 'Login link sent to your email'
  end
end
```

## Create the LoginMailer

We defined the LoginMailer, now we need to create it.
Create a file in app/mailers/login_mailer that will look like this

```rb
class LoginMailer < ApplicationMailer
  def send_email(user, url)
    @user = user
    @url  = url

    mail to: @user.email, subject: 'Sign in into mywebsite.com'
  end
end
```

We need to create the view of the letter. Create a new file app/views/login_mailer/send_email.html.erb

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

## (Optional) Configure LetterOpener

LetterOpener is a great gem that allows us to view emails that have been sent in development.

Once again, add the gem in the Gemfile and bundle

```rb
gem "letter_opener", :group => :development
```

```console
bundle
```

In development.rb, include these two lines

```rb
config.action_mailer.delivery_method = :letter_opener
config.action_mailer.perform_deliveries = true
```

We should now be able to send and view the emails we send!

## Sessions controller

```rb
class SessionsController < ApplicationController
  def create
    # we don't log in the user if a login token has expired 
    user = User.where(login_token: params[:login_token])
                .where('login_token_valid_until > ?', Time.now).first

    if user
      # nullify the login token so it can't be used again
      user.update!(login_token: nil, login_token_valid_until: 1.year.ago)

      # sorcery helper which logins the user
      auto_login(user)

      redirect_to root_path, notice: 'Congrats. You are signed in!'
    else
      redirect_to root_path, alert: 'Invalid or expired login link'
    end
  end

  def destroy
    # sorcery helper which logouts the user
    logout

    redirect_to root_path, notice: 'You are signed out'
  end
end
```

Congrats! You now have a passwordless authentication

## Final touches - UsersController and user views

Let's finish this article by actually doing something with the passwordless auth.

Update your UsersController which allows us to update the user name.

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

Update views/users/index.html.erb

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

To make sure that the authentication works, go to incognito and enter the edit page URL. You shouldn't be able to access the page unless you are logged in with the correct account.

[Source code for this article](https://github.com/kb1995/railsjunior-passwordless-auth)