---
layout: blog
slug: upgrade-to-rails-6
title: How to upgrade from Rails 5.2 to Rails 6
subtitle: Step by step process on how I upgraded my client's websites from Rails 5.2 to Rails 6
date: 2019-10-01T7:00:00.000+00:00
category: rails

---

Over the last month, I had to upgrade my client's Rails websites to Rails 6. One of the apps was on Rails 5.2 and the other was on an older version - Rails 5.0.6.

I knew that it won't be an easy process, so I decided to document this so hopefully, this will help anyone else who needs to upgrade.

At the bottom of the article, I included all sources that helped me during this task.

## Why you should upgrade to Rails 6

<ul>
  <li><b>New tools under your belt</b> - Action text, Action mailbox, testing improvements, multiple databases and more - a lot of new feature that will be useful in the future</li>
  <li><b>Improves security</b> - several security updates are introduced in Rails 6 - the new Host Authorization middleware and improved security around cookies</li>
  <li><b>Gem upgrades</b> - Rails is not the only gem that receives new features - in time gems might adopt new features which are dependent on Rails 6</li>
  <li><b>Makes future upgrades easier</b> - the older your Rails application is, the harder it is to upgrade. Upgrade now, so the future developer (who might be you) doesn't despise you</li>
  <li><b>Webpack as the default</b> - I already had webpack installed, but this makes it official - webpack will replaces asset pipeline. Even though you will see articles on the internet saying you should move images and stylesheets to webpack as well, I would keep everything in the asset pipeline except Javascript (I've tried to move everything in the past and trust me - it's not worth it)</li>
</ul>

## Step 1: Update to Ruby version 2.5 or higher

Rails 6 requires Ruby 2.5 or newer. Depending on when the last time you updated your Ruby version was, this step might be straightforward or you might encounter some errors. <a href="https://www.ruby-lang.org/en/downloads/releases/" target="_blank">The history of Ruby releases</a> might be a helpful reference. I recommend you to start updating at every minor release (second number in the Ruby version) and inspect if your your website is still running as expected at every update.

It didn't take me much time to complete this step. My apps were already at a Ruby version 2.6.2, which was released earlier in 2019, so I just decided to leave it at that. Rails 6 will work perfectly fine at that version, so I can update to the latest Ruby version as a separate task.

## Step 2: Upgrade your Rails version to 5.2.3 - the latest version of the 5.2 series

Before making the big jump, make sure to upgrade to Rails 5.2.3 and that everything is running as expected at at this version.

If your Rails version is 5.2, it would be a painless process. That was the case for one of my apps, but the other app was at Rails 5.0.6. It took me a bit of time to fix several gem dependencies and deprecated methods, but this helped me prepare for the next step.

## Step 3: Update to Rails 6

It's time! Replace your current version of Rails in the Gemfile to

```rb
gem 'rails', '~> 6.0'
```

and in your terminal, execute

```terminal
bundle update rails
```

## Step 4: Fix Gemfile dependencies

Can you spot which one needs a version bump? 

```
Bundler could not find compatible versions for gem "activesupport":
  In Gemfile:
    delayed_job_active_record was resolved to 4.1.3, which depends on
      delayed_job (>= 3.0, < 5) was resolved to 4.1.7, which depends on
        activesupport (>= 3.0, < 5.3)

    jbuilder (~> 2.5) was resolved to 2.9.1, which depends on
      activesupport (>= 4.2.0)

    kaminari (~> 1.1) was resolved to 1.1.1, which depends on
      activesupport (>= 4.1.0)

    lograge (~> 0.11.2) was resolved to 0.11.2, which depends on
      activesupport (>= 4)

    rails (~> 6.0) was resolved to 6.0.0, which depends on
      activesupport (= 6.0.0)

    rspec-rails (~> 3.5) was resolved to 3.8.2, which depends on
      activesupport (>= 3.0)
```

Rails will output a long list of gems, but in reality we only need to update a small subset of this list.

In this error, this version of the <mark>delayed_job_active_record</mark> gem is dependent to activesupport with a maximum version < 5.3.

Therefore, we need to upgrade to a version that supports activesupport. We can go to the <a href="https://rubygems.org/gems/delayed_job_active_record/" target="_blank">rubygems website</a> and find a compatable version with active support 6.0. For this gem, version 4.1.4 supports active support 6.0. 

Great news! We can just upgrade the Gemfile with this version of the gem and run <mark>bundle update rails</mark> again.

<hr>

After this issue was fixed I got another dependency error - this time with the railties gem.

```
Bundler could not find compatible versions for gem "railties":
  In Gemfile:
    devise was resolved to 4.6.2, which depends on
      railties (>= 4.1.0, < 6.0)

    font-awesome-rails (~> 4.7.0) was resolved to 4.7.0.5, which depends on
      railties (>= 3.2, < 6.1)

    jquery-rails was resolved to 4.3.5, which depends on
      railties (>= 4.2.0)

    fae-rails (~> 2.1) was resolved to 2.1.0, which depends on
      jquery-ui-rails (~> 6.0.1) was resolved to 6.0.1, which depends on
        railties (>= 3.2.16)

    lograge (~> 0.11.2) was resolved to 0.11.2, which depends on
      railties (>= 4)

    money-rails (~> 1.10) was resolved to 1.13.2, which depends on
      railties (>= 3.0)

    rails (~> 6.0) was resolved to 6.0.0, which depends on
      railties (= 6.0.0)

    rails-ujs was resolved to 0.1.0, which depends on
      railties (>= 3.1)

    react-rails (~> 2.4) was resolved to 2.5.0, which depends on
      railties (>= 3.2)

    sass-rails (~> 5.0.0) was resolved to 5.0.7, which depends on
      railties (>= 4.0.0, < 6)

    web-console (>= 3.3.0) was resolved to 3.7.0, which depends on
      railties (>= 5.0)
```

Did you spot it? We need to bump our devise and sass-rails versions. We do that and once again we run <mark>bundle update rails</mark>.

<hr>

But wait. Now we are getting this error?

```terminal
    fae-rails (~> 2.1) was resolved to 2.1.0, which depends on
      devise (~> 4.6.2) was resolved to 4.6.2, which depends on
        railties (>= 4.1.0, < 6.0)
```

In order to use the version of fae-rails which supports Rails 6, I need devise 4.6.2, but devise 4.6.2 doesn't work with railties 6 (which we need for Rails 6). Now what??

## What if a gem hasn't released an update for Rails 6?

If you encounter this problem, go to that gem's Github repo and search for issues or Pull Requests. In the majority of cases, someone else has already done the upgrade.

To fix fae's dependency issue, I went to the Github repo and saw that there is a pull request that bumps the version of Rails, but it was not merged to master as of yet. Some tests were failing as well. The tests looked like were failing because it's an older version of Ruby. 

Nevertheless, I will use it by doing the following

```terminal
gem "fae-rails", :git => "https://github.com/robikovacs/fae.git", :ref => "2f859885475adf9389c4d80f54d7c60b357625b9"
```

I need to take this step with caution. Fae and Devise are an important part of the app, so I will make a note of this and look if there has been any merged changes before I deploy to production.

Devise and Fae are popular gems, so I am confident that it will get merged into master in no time.

<hr>

Continue doing this until you see the green message that indicates that you are finally done with this step.

```terminal
Bundle updated!
```

## Step 5: Run rails app:update

In your terminal, run

```terminal
rails app:update
```

This will help you integrate your rails app with the new config settings. This command will overwrite a lot of your perfectly fine code. That's why I recommend you to select the <mark>m</mark> option to merge the files. Afterwards, select <mark>y</mark> for every file. Finally, use a merge tool to inspect what changes were implemented.

Using this approach, you will see exactly what is updated by this command so you can decide what you want to keep in and what to replace.

## Step 6: Uncomment defaults in new_framework_defaults_6_0.rb

Go to <mark>new_framework_defaults_6_0.rb</mark>. I suggest to read the comments - there are several new defaults which are recommended to uncomment once you are confident that your app is stable on Rails 6. I did as it was suggested.

## Step 7: Check out Rails 6 release notes

The next step is to go to <a href="https://edgeguides.rubyonrails.org/6_0_release_notes.html" target="_blank">Rails 6 release notes</a> and double check for any removals and deprecations. If any of them impact your code - fix them!

## Step 8: Start testing your app

After you fix your tests and everything is running well locally, push to staging and go around the website to see if something is broken.

I recommend doing this for a couple of days while implementing other features. This should be enough time to find the majority of issues that might have happened during the upgrade. 

## Further reading

<a href="https://selleo.com/blog/how-to-upgrade-to-rails-6" target="_blank">https://selleo.com/blog/how-to-upgrade-to-rails-6</a> <br>
<a href="https://guides.rubyonrails.org/upgrading_ruby_on_rails.html#upgrading-from-rails-5-1-to-rails-5-2-bootsnap" target="_blank">https://guides.rubyonrails.org/upgrading_ruby_on_rails.html#upgrading-from-rails-5-1-to-rails-5-2-bootsnap</a> <br>
<a href="https://guides.rubyonrails.org/configuring.html" target="_blank">https://guides.rubyonrails.org/configuring.html</a> <br>
<a href="https://edgeguides.rubyonrails.org/6_0_release_notes.html" target="_blank">https://edgeguides.rubyonrails.org/6_0_release_notes.html</a> <br>
<a href="https://reinteractive.com/posts/26-top-5-reasons-why-you-should-upgrade-your-rails-application" target="_blank">https://reinteractive.com/posts/26-top-5-reasons-why-you-should-upgrade-your-rails-application</a> <br>
<a href="https://www.toptal.com/ruby-on-rails/rails-6-features" target="_blank">https://www.toptal.com/ruby-on-rails/rails-6-features</a> <br>






