---
layout: blog
slug: current-attributes
title: Rails' CurrentAttributes
subtitle: Rails 5.2 adds support for CurrentAttributes which give you access to global variables in your application.
date: 2020-02-26T7:00:00.000+00:00
category: rails
---

## Overview of CurrentAttributes

Active Support CurrentAttributes adds <mark>global per-request attributes</mark> to your Rails app. To get started, create a Current class inside your models folder. You can assign any model in your application as an attribute to use as a global variable. We will discuss it later, but you should only use CurrentAttributes for your most important and widely used models within your application, for example, Project or Team.

Check out the <a href="https://github.com/rails/rails/commit/24a864437e845febe91e3646ca008e8dc7f76b56" target="_blank">commit</a> and  <a href="https://github.com/rails/rails/pull/29180" target="_blank">pull request</a> created by DHH to learn more about how CurrentAttributes work under the hood. You don't have to understand how the entire code works, but I find it useful to see an overview of how the "Rails magic" is created.  

In the example below, I have assigned my User model to be a CurrentAttribute.

```ruby
# app/models/current.rb
class Current < ActiveSupport::CurrentAttributes
  attribute :user
end
```

You can then set your CurrentAttributes simply by assigning them to an object

```ruby
Current.user = current_user
```

Once you set this, you will be able to access your user object globally (for example, within views and models) without the need to declare them all the time.

## Example: create a subdomain for your projects and use that subdomain to assign it to CurrentAttributes

Best way to learn about CurrentAttributes is to create a quick example illustrating a use case for it. For our example, we want to have projects names/slugs as our subdomain. For example <mark>project-name.lvh.me:3000</mark>.

Create a Project with a name, subdomain and a user_id fields. Users have many projects and a project belongs to a user.

In order to complete this example, let's sort out the subdomains. This is not a tutorial on subdomains, so the code is limited to just showcase CurrentAttributes.

```ruby
# routes.rb
require "subdomain_required"

Rails.application.routes.draw do
  #... 

  constraints(SubdomainRequired) do
    resources :projects

    # root
    root "projects#show", as: :projects_root
  end

  #
  root to: 'home#index'
end
```

```ruby
# lib/subdomain_required.rb
class SubdomainRequired
  def self.matches?(request)
    request.subdomain.present? && request.subdomain != 'www'
  end
end
```

To share a devise user session across subdomains:

```ruby
# initializer/session_store.rb

# replace current_attributes_example with your app name
Rails.application.config.session_store :cookie_store, key: '_current_attributes_example_session'
```

Create the Current class as the example above and assign <mark>project</mark> as the attribute. 

```ruby
# app/models/current.rb
class Current < ActiveSupport::CurrentAttributes
  attribute :project
end
```

Then we can create a before_action in the ApplicationController where we assign <mark>Current.project</mark> to the project depending on the subdomain.

```ruby
  before_action :set_project

  private

  def set_project
    Current.project = current_user.projects.find_by_subdomain(request.subdomain) if request.subdomain.present?
  end
```

You can then use <mark>Current.project</mark> within our application and this will be scoped based on our subdomain.

You can then list your user projects and link them to the subdomain

```erb
<% current_user.projects.each do |project| %>
  <%= link_to project.name, projects_roots_url(subdomain: project.subdomain) %>
<% end %>
```

You will get redirected to the subdomain and our Current.project will have our relevant project.

```
<h1>Welcome to <%= Current.project.name %></h1>
```

Our project is now present globally within our app so we can continue writing our logic using Current.project instead of querying the project in every controller, model and view.  

## Beware of potential problems when using CurrentAttributes

I came across <a href="https://ryanbigg.com/2017/06/current-considered-harmful" target="_blank">this article</a> which warns of the potential dangers of using CurrentAttributes and global state in general.

Important note it raises that you should be aware of - <mark>avoid using CurrentAttributes in background jobs</mark>. Instead, pass your CurrentAttributes object's id as a parameter to your background job and then find the record within. This makes your background jobs explicit and avoids the danger of using the wrong record to perform the job.

```ruby
# don't pass the object itself
def perform(user)
  # user is Current.user
end

# pass the id of Current.user instead
def perform(user_id)
  user = User.find user_id
end
```

DHH recommends using CurrentAttributes for <mark>a few, top-level globals, like account and user.</mark> Consider using CurrentAttributes if you are going to use this attribute in most of your actions. Otherwise, CurrentAttributes will become anti-productive and introduce problems within your application.

In conclusion, CurrentAttribute can be a great help to your application, but you should use it with caution and you should be aware of the dangers of using a global variable.