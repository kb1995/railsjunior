---
layout: blog
slug: cookie-policy-popup
title: Create a simple cookie policy popup without using any libraries
subtitle: A tutorial teaching you how to create a cookie policy consent popup without using any libraries
date: 2019-11-21T7:00:00.000+00:00
category: Javascript
---

Here is a quick and easy way to create a cookie policy popup without using any libraries.

In this tutorial, I only use a bit of jQuery and Bootstrap. But they don't count, right?! You can adjust the code bellow accordingly if you are not using them.

## The HTML

We have a <mark>cookie-overlay</mark> class and we hide the popup by default using Bootstrap.

We also have a button to accept the cookies as well as a close button if they don't want to accept. If they don't accept it, they will be redirected to accept it again next time they visit the website. You can expand this tutorial if you wish to cover this scenario as well.

```erb
<div class="cookie-overlay p-4 d-none">
  <div class="d-flex">
    <div class="mr-3">By clicking the "Accept" button below, you agree to our <%= link_to 'Cookie Policy', cookies_url, target: '_blank' %>.</div>
    <%= image_tag  "close.png", height: '21', class: 'close-cookies' %>
  </div>
  <button class="btn btn-primary mt-3 accept-cookies">Accept</button>
</div>
```

<ul>
  <li>Define a wrapper with class <mark>cookie-overlay</mark> which we will use to style the box.</li>
  <li>Include text, a link to your Cookie Policy page, a close button if they don't want to accept the cookies and an Accept button.</li>
</ul>

## The CSS

Next, let's make the popup look pretty.

```css
.cookie-overlay {
  position: fixed;
  bottom: 1rem;
  left: 1rem;
  background: #fff;
  z-index: 2051;
  line-height: 20px;
  font-size: 14px;
  border-radius: 6px;

  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);

  > div:first-child {
    width: 458px;
  }

  a {
    text-decoration: underline;
  }

  .close-cookies {
    cursor: pointer;
  }

  @include media-breakpoint-down(md) {
    bottom: 0;
    left: 0;
    right: 0;
    border-radius: 0;

    > div:first-child {
      width: auto;
    }
  }
}

```

You are welcome to use your creativity here. 

Using the styling above will make the popup appear on the bottom left part of the screen on desktop, whilst on mobile, it will cover the entire width of the bottom part of the screen real estate.

## The Javascript

Finally, for the most important ingredient in our tutorial.

```javascript
// cookie policy
$(document).on('ready', function() {
  if (document.cookie.indexOf("accepted_cookies=") < 0) {
    $('.cookie-overlay').removeClass('d-none').addClass('d-block');
  }

  $('.accept-cookies').on('click', function() {
    document.cookie = "accepted_cookies=yes;"
    $('.cookie-overlay').removeClass('d-block').addClass('d-none');
  })

  // expand depending on your needs
  $('.close-cookies').on('click', function() {
    $('.cookie-overlay').removeClass('d-block').addClass('d-none');
  })
})
```

<a href="https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie" target="_blank">Document.cookie</a> lets you read and write cookies associated with your browser. It's really easy and intuitive to use as shown in the code above.

You can read all cookies using the command <mark>document.cookie</mark> and you can add a new cookie using <mark>document.cookie = newCookie</mark>.

A few things to note:

<ul>
  <li>On page load, we check if <mark>document.cookie</mark> includes <mark>accepted_cookies=</mark>.</li>
  <li>If it's not present, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf" target="_blank">.indexOf</a> would output -1. If it is present, it would output 0 or higher. In conclusion, if the we can't find <mark>accepted_cookies</mark> inside document.cookie, we show the cookie policy popup.</li>
  <li>The user has two options. They can click on <mark>.close-cookies</mark> which will close the cookies popup, but it will appear once again next time they visit the website. You can expand this example to cover this scenario (for example, disable cookies for this user).</li>
  <li>On the other hand, the user can also click <mark>.accept-cookies</mark> button, which will add <mark>accepted_cookies=yes;</mark> to document.cookie and hide the popup. Next time the user comes to the website or goes to another page, <mark>document.cookie</mark> will include <mark>accepted_cookies</mark>. Therefore, the popup won't be displayed to the user.</li>
  <li>You can move <mark>document.cookie = "accepted_cookies=yes;"</mark> within the if statement so the user automatically accepts whenever they visit the website. If you decide to do that, you can probably remove the Accept button as it is not needed anymore.</li>
</ul>






