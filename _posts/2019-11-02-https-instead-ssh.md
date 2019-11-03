---
layout: blog
slug: use-https-instead-of-ssh
title: Set up a https remote when ssh is not working
subtitle: Create a https remote so you can pull and push code to Github when your internet connection is not secure and ssh doesn't work.
date: 2019-11-02T7:00:00.000+00:00
category: devops

---

Last week I was working from a café and I had to pull and push code to Github. But I got the following error

```terminal
ssh: connect to host github.com port 22: Operation timed out
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

The basic explanation for this error is that I was on a unsecured network, so the git software was not able to connect to Github through ssh. But I had to push my code. I also didn't want to change my permanent settings on my mac to use https instead of ssh just because I was working from a coffee shop today (and then have to switch back to ssh later in the day).

The solution that I came up with is creating a remote called <mark>origin-https</mark> which uses https while my <mark>origin</mark> would still use ssh. Let's dive in on how you can set it up yourself.

## Set up origin-https remote

To add a new remote, simply use the command

```terminal
git remote add origin-https https://github.com/XXX
```

<mark>origin-https</mark> is the name of the new remote followed by the url of your repo. Notice that I have used the https url instead of the ssh. You can get it this link from the Github repo.

## Log in into Github using your personal access token

You might be asked to login using your username and a password. Confusingly enough, the password you need to enter is not your Github password. 

Instead, it's your <mark>personal access token</mark>. This token is used to perform git operations over https in the command line. To log in, you can follow <a href="https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line" target="_blank">this article by Github</a> which has done a great job at explaining how to create a personal access token and log in from your console.

If you are not prompted for your username and password, your credentials may be cached on your computer.

## Pull or Push from origin-https

Now that we have successfully created the new remote, we can pull or push using the commands

```terminal
git pull origin-https BRANCH-NAME
git push origin-https BRANCH-NAME
```

Your code is pushed! 🎉You can now use https whenever you are out drinking coffee and ssh whenever you are at home or in the office.