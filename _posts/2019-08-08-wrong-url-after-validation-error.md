---
layout: blog
slug: wrong-url-validation-error
title: Why a Rails form validation error renders the wrong URL and how to fix it
date: 2019-08-08T6:00:00.000+00:00
category: rails

---

Before I encountered the following problem, I didn't pay attention to render and redirect_to. I just assumed how they work and let Rails do its magic. But even though correct, the Rails magic confused me whenever I encountered this "bug". I decided to write a short article addressing the simple "cheat" I used. 

The following problem is very obscure, but here it comes.

I have used edit/update actions for the following example, but you can apply the same technique to your exact situation.

## The problem

For the sake of this article, I have a normal User controller with a show, edit and update actions.

```rb
class UserController < ApplicationController
  def show
    @user = User.first # for testing purposes
  end

  def edit
    @user = User.first # for testing purposes
  end

  def update
    @user = User.first # for testing purposes

    if @user.update user_params
      flash[:notice] = 'Details updated.'
      redirect_to action: :show
    else
      flash[:notice] = 'Wrong details. Try again'
      render :edit
    end
  end

  private
    def user_params
      params.require(:user).permit(:name, :email)
    end
end
```

Routes.rb defines the user resource for these actions

```rb
  resources :user, only: [:show, :edit, :update]
```

which creates the following routes

```console
Prefix    Verb   URI                                                                                      Controller#Action
edit_user GET    /user/:id/edit(.:format)                                                                 user#edit
user      GET    /user/:id(.:format)                                                                      user#show
          PATCH  /user/:id(.:format)                                                                      user#update
          PUT    /user/:id(.:format)                                                                      user#update
```

<b>Important! </b>Notice that the paths for the <mark>show</mark> and <mark>update</mark> are the same, but the method verbs are different.

<hr>

The purpose of the update action is to redirect to the show action if the update has been successful or render the edit form with the validation errors. 

In order to test if it works, I go to <mark>/user/1/edit</mark> and I enter the false information, so that the user update fails.

As intended, the form is rendered with the error information. But the new URL is <mark>/user/1</mark>.

<mark>The problem I had is that I refresh the page, I go to the show action - I don't stay in the edit action.</mark>

## Why this happens?

Whenever you press the Submit button of your form, you are being redirected to <mark>/user/1</mark> with a PUT verb, which routes to the update action as instructed by our routes.rb file.

If the update action fails the validation, it renders the edit form again - it does not redirect!

Therefore, the URL remains unchanged - <mark>/user/1</mark>.

## The solution I used

At first, we might think that we can change the <mark>render</mark> to <mark>redirect_to</mark> - but then we won't get the validation error. So this is not an option.

I decided to update the routes to the following

```rb
resources :user, only: [:show, :edit]
put '/user/:id/edit', to: 'user#update', as: 'update_user'
```

This maps the PUT http method to <mark>/user/:id/edit</mark>, so even if the validation fails, I still redirect to the edit action.

If the user refreshes the page, it doesn't matter - the URL still is <mark>/user/:id/edit</mark>.

The URL of the form needs to be updated to the new URL. In this example, we need to change the form from

```erb
<%= form_for @user, url: user_path(@user), method: :put do |f| %>
```

to

```erb
<%= form_for @user, url: update_user_path(@user), method: :put do |f| %>
```

TODO: Source code for this article

## Further reading

[https://github.com/turbolinks/turbolinks/issues/60](https://github.com/turbolinks/turbolinks/issues/60)
[https://stackoverflow.com/questions/31059992/ruby-on-rails-use-slug-instead-of-id-on-resource-routes](https://stackoverflow.com/questions/31059992/ruby-on-rails-use-slug-instead-of-id-on-resource-routes)
[https://guides.rubyonrails.org/routing.html](https://guides.rubyonrails.org/routing.html)
