---
layout: blog
slug: debounce-throttle-stimulus
title: Debounce and throttle in Stimulus
subtitle: Debounce and throttle give us control over the rate at which a function is called. But do you call them in your stimulus controllers? 
date: 2020-04-03T7:00:00.000+00:00
category: stimulusJS
---

## How are debounce and throttle used in Javascript?


Debounce and throttle are techniques that control how many times a function is executed in a given amount of time.

For example, you want to autosave a form while the user is typing. You can attach the autosave action on a keyUp event. But sending an ajax request every time a user presses a button could easily overload the server. 

A good solution would be to use debounce to limit the number of times the autosave function is executed. For example, submit the form only after the user has stopped typing for 2 seconds. This means that the user won't submit the form while he/she is typing faster than two second between different keyUp events. 

Once they have taken a small rest and stopped typing for longer than 2 seconds, send the ajax request. This will reduce the load on your servers by a good amount.

## What is the difference between debounce and throttle?

Debounce groups multiple calls into a single one. It enforces a function not to be called until a given period of time has passed, for example "Execute this function if 1 second has passed without it being called".

Throttle doesn't allow a function to execute more than once in a given period, for example "Execute this function at most once every second".

## How to implement debounce and throttle in Stimulus?

I use the lodash implementation of debounce and throttle. First, import the function and assign it to a variable outside your stimulus controller

```javascript
import { Controller } from "stimulus"
let debounce = require('lodash/debounce');
// or
let throttle = require('lodash/throttle');

export default class extends Controller {
  // ...
}
```

You can then attach a stimulus method to any javascript event as you would normally

```html
<input data-action="keyup->form#autosave" />
```

In your stimulus initialize method you can bind your debounce/throttle function to your stimulus action.

```javascript
initialize(){
  this.autosave = debounce(this.autosave, 2000).bind(this)
}

autosave(){
  // autosave logic here...
}
```

And that's about it. You can now easily debounce and throttle your stimulus actions. 👨‍🎤