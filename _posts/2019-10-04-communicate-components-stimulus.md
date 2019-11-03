---
layout: blog
slug: stimulusjs-create-custom-events
title: Communicating between Stimulus controllers using custom events
date: 2019-10-04T6:00:00.000+00:00
category: stimulusJS

---

There isn't a lot of information online about how you should go about communicating between Stimulus controllers. There is one <a href="https://github.com/stimulusjs/stimulus/issues/35" target="_blank">issue on Github</a> which explains how you should do it using <mark>this.application.getControllerForElementAndIdentifier</mark> but I was not able to make it work using this approach.

In this article I will teach you how to achieve this using another technique - create a custom event in one controller which triggers an action in another controller.

## Create the custom event

In your stimulus controller, you can create a custom event using:

```js
const event = new CustomEvent("update-map")
window.dispatchEvent(event)
```

Let's brake down this code.

1. You define a constant <mark>event</mark> using the CustomEvent constructor. This object represents an event which will take place in the DOM. The parameter represents the <mark>name</mark> of the event. You can name your event however you like to explain your own unique situation. You can also pass a second parameter, if you wish to include additional information as part of your event. You can read more about it <a href="https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent#Syntax" target="_blank">here</a>.
3. Dispatch the event to the DOM and invoke the affected EventListeners (in our case, the <mark>data-action</mark>)

## Link it to another controller using a data-action

In your HTML, define the following <mark>data-action</mark> inside your receiving Stimulus controller.

```erb
data-action="update-map@window->map#updateHotspotLocation"
```

This will trigger the <mark>updateHotspotLocation</mark> method in <mark>map_controller.js</mark>.

## And done!

You can now head over your receiving controller and continue coding.

## Further reading

[https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)
[https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent)
[https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent)
