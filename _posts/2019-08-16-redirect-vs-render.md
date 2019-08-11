---
layout: blog
slug: redirect-vs-render
title: Theory&#58; Redirect vs render
date: 2020-08-08T6:00:00.000+00:00
category: rails

---

## Redirect

Redirect is used to tell the browser to issue a new request

Errors won't be displayed

But if you're using redirect_to, when the user refreshes the page, it will just request that same page again.


## Render

You can display notices and errors using renders

If you're using render, when the user refreshes the page, it will submit the previous POST request again. This may cause undesired results like duplicate purchase and others.

In Rails, when a model fails to be saved, render is used to redisplay the form with the same entries that was filled previously. This is simpler because if you use redirect, you'll have to pass the form entries either using parameters or session. The side effect is that if you refresh the browser, it will try to resubmit the previous form entries. This is acceptable because it will probably fail the same way, or if it's successful now, it was what the user should expect in the first place anyway.

## Further reading

[https://culttt.com/2015/04/22/what-are-symbols-in-ruby/](https://culttt.com/2015/04/22/what-are-symbols-in-ruby/)