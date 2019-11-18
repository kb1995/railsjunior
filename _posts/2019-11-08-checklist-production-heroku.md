---
layout: blog
slug: rails-deploy-production-heroku
title: Checklist for deploying your Rails app to production on Heroku
subtitle: The things that you need to do before you deploy. This is my checklist that I follow when I deploy a new feature of my Rails app to production on Heroku
date: 2019-11-08T7:00:00.000+00:00
category: devops

---

Ever since I started working with my client, I had to deploy to staging and then production. On staging, even if I deploy a breaking change or I mess up something in during deployment, I have time to fix it, as nobody but me and my client will see the mess I have created.

But production is different - I have to be sure that things are going to work after I deploy. So I decided to create a quick checklist which I can follow every time I deploy my Rails app to production. Take notes.

<mark>I will assume that you are doing database migrations.</mark>

## 1. Put your app to maintenance mode

It is not necessary to do this if you are not doing any database migrations.

But if you are, you don't want any information to be lost during the deployment process. It is easy to turn maintenance mode on - head over <mark>Heroku -> Settings -> Maintenance Mode -> Switch ON</mark>.

## 2. Make a backup of your Postgres database

Go to your production project on <mark>Heroku -> Resources -> Postgres database -> Durability -> Create a manual backup -> Download</mark>

This will ensure you that you have the latest version of your database after you have turned maintenance mode on. Even if you do something terribly wrong, you can go back and upload the latest stable version of your database. Then you can try again!

## 3. Merge develop to master

First checkout to master

```terminal
git checkout master
```

and then merge from develop to master

```terminal
git merge develop
```

Hopefully there won't be any merge conflicts.

## 4. Push to heroku

```terminal
git push heroku master
```

If you have multiple heroku apps (for example, staging and production), you will need to specify the name of the app using <mark>--app app_name</mark>.

To get a list of your heroku apps and their names, run

```terminal
heroku apps
```

## 5. Run migrations

Now that you have pushed, you can migrate

```terminal
heroku run rake db:migrate
```

## 6. Run any tasks

If you have any tasks that need to be ran, now it is a good opportunity to do so.

```terminal
heroku run rake my_namespace:task_name
```

## 7. Update environment variables

Now that you have pushed your commits, migrated and run your tasks, you can go in your settings panel on Heroku, press <mark>Reveal Config Vars</mark> and add any new environment variables that you might have added since your last deployment.

## 8. Turn off maintenance mode

Now that everything is ready, you can go in your settings panel and turn the maintenance mode off in the same way you turned it on.

## 9. Test that the new feature is working

Everything should be up and running. But I always like to go and test the app is working as expected.

That's it! You can now push to deployment with confidence!

