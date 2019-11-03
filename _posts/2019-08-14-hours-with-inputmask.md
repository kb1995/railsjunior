---
layout: blog
slug: hour-input-inputmask
title: How to create hours input with Input Mask and stimulusJS
subtitle: if you are building an hour input, you can use input mask and stimulus to create a nice UI for your user.
date: 2019-08-13T6:00:00.000+00:00
category: stimulusJS

---

I recently needed to implement an hours input. <a href="https://www.npmjs.com/package/inputmask" target="_blank">Inputmask</a> comes to the rescue whenever you want to make an input look pretty. 

I remembered that I had to do this exact same thing many months ago for different project. 

I also remember that it took me ages to implement the solution, even though it might look relatively straightforward.

## Install StimulusJS

It's easy to add StimulusJS to your Rails application (assuming that you already have webpacker)

```terminal
bundle exec rails webpacker:install:stimulus
```

## Install Input Mask

Install <a href="https://www.npmjs.com/package/inputmask" target="_blank">inputmask</a> using

```terminal
npm i inputmask
yarn install
```

## The view - a general example

Create an input and link it to the Stimulus controller, which we will create in the next step.

I also included a simple form with a date input, which we use to update to update the field in our table.

We update the form on every keyup on the inputmask input. 

```erb
<div data-controller="hour">
  <input type="text" data-target="hour.input" data-action="keyup->hour#getTime" id="inputmaskInput">
  
  <%= simple_form_for @user, url: root_path do |f| %>
    <%= f.input :date, label: "Simple form date", include_blank: true %>
  <% end %>
</div>
```

## The Javascript - working with stimulusJS

Create a <mark>hour_controller.js</mark>

```js
import { Controller } from "stimulus"
import Inputmask from "inputmask";

export default class extends Controller {
  static targets = ["input"]

  initialize(){
    // default placeholder
    var str = '09:00'

    // load values from form input
    var hours = document.getElementById("user_date_4i").value
    var minutes = document.getElementById("user_date_5i").value

    if (hours.length && minutes.length){
      str = hours
      str = hours+":"+minutes
    }

    // apply Regex to the input mask, so you can only write times in the 24 hour format
    Inputmask({regex: "([01]\\d|2[0-3])(:)\:?([0-5]\\d", placeholder: str, clearMaskOnLostFocus: false}).mask(this.inputTarget);
  }

  getTime(){
    // get time from input on keyup and apply it to your simple form input
    var value = document.getElementById("inputmaskInput").value
    var hours = value.substring(0,2)
    var minutes = value.substring(3,5)
    document.getElementById("user_date_4i").value = hours
    document.getElementById("user_date_5i").value = minutes
  }
}
```

<mark>document.getElementById("user_date_4i")</mark> and <mark>document.getElementById("user_date_5i")</mark> will be different depending on your form. You can see what are the IDs in the dev tools of your browser.

The initialize() method is executed only we load the page.

We define a variable <mark>str</mark> for our default time.

We then see if we already have values in our simple form input (in the case of an edit form). If that's the case, update the str variable with hours and minutes, otherwise keep it the same as originally defined.

Next, it's time for the Inputmask magic. We define a regex expression, which only allows valid 24 hour clock entries. Finally, we applied it to our inputTarget.

The getTime() action is only executed on a keyup of the inputmask input. We get the values for hours and minutes and apply it to our simple form input.

## AM/PM

It's easy to convert the 24 clock format to an AM/PM format. 

Just update the <mark>initialize</mark> action in this way

```js
initialize(){
  // default placeholder
  var str = '09:00 \\AM'

  // load values from form input
  var hours = document.getElementById("user_date_4i").value
  var minutes = document.getElementById("user_date_5i").value

  if (hours.length && minutes.length){
    str = hours
    str = hours+":"+minutes+"\\AM"
  }

  // apply Regex to the input mask, so you can only write times in the 24 hour format
  Inputmask({regex: "((1[0-2]|0?[1-9]):([0-5][0-9] (AM))", placeholder: str, clearMaskOnLostFocus: false}).mask(this.inputTarget);
}
```

Change AM to PM if you wish to use PM instead.

<a href="https://github.com/kb1995/railsjunior-hours-inputmask" target="_blank">Source code for this article</a>

## Further reading

[https://medium.com/cedarcode/installing-stimulus-js-in-a-rails-app-c8564ba51ea2](https://medium.com/cedarcode/installing-stimulus-js-in-a-rails-app-c8564ba51ea2)