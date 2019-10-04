---
layout: blog
slug: letteropener-gem-not-working
title: Why Letter opener gem doesn't open your letters - tldr; debug with .deliver!
date: 2019-09-13T6:00:00.000+00:00
category: rails

---

<a href="https://github.com/ryanb/letter_opener" target="_blank">Letteropener</a> is a great gem for opening letters locally. It has one of the easiest configurations out of any gem. Up until now, it was working as intended when all of a sudden it just stopped working.

Earlier this week, I was developing a new feature for my client. I was almost done with it - I just needed to see how the final email would look like. Easy enough - letteropener to the rescue. But for some reason, I didn't receive my emails as usual. 

The usual helpers - Stack Overflow and Github Issues were of no use.

## Debug, Debug, Debug 🔬

I had no idea that you can do <mark>ExampleMailer.deliver!</mark> instead of <mark>.deliver_later</mark> or <mark>.deliver</mark>.

This gave me the error

```ruby
letter opener SMTP To address may not be blank
```

Finally - something to work with!

## My query for my TO email was wrong 🤦‍♂️

I instantly realized what's wrong - I didn't define my query for my recipient in the correct way. LetterOpener was working as intended, it was my code that was wrong, but Rails didn't let me know until I used <mark>.deliver!</mark>


## Further reading

[https://til.codes/letter-opener-is-not-processing-emails-in-rails-app/](https://til.codes/letter-opener-is-not-processing-emails-in-rails-app/)