---
layout: post
title:  "Enable vs Disable - jQuery Mobile"
categories: jquery, jquery mobile
---

How to enable and disable various components in jQuery Mobile? Following are my tips.

### Button

To disable a button, simply add the 'disabled' attribute to it:

```html
<button id="btn" type="button" disabled>Button</button>
```

To disable a button, simply add the 'disabled' attribute to it:

```javascript
$('#btn').button('disable'); // Disable button
$('#btn').button('enable');  // Enable button
```

The same applies to a submit button. Adding the 'disabled' attribute to the submit button disables it.

```html
<input id="submit" type="submit" value="Submit" disabled>
```

Or we're able to manipulate the submit button via Javascript:

```javascript
$('#submit').button('disable'); // Disable submit button
$('#submit').button('enable');  // Enable submit button
```

As far as link button is concerned, since anchors don't natively have any disabled state, jQuery Mobile group decides not to support disable and enable anchors like buttons. Therefore, we have to apply 'ui-disabled' class to the anchor to achieve the same effect.

```html
<a id="anchor" href="#" data-role="button" class="ui-disabled">Link button</a>
```

Here's how to do via Javascript:

```javascript
$('#anchor').addClass('ui-disabled');     // Disable anchor button
$('#anchor').removeClass('ui-disabled');  // Enable anchor button
```

### Form

It's extremely easy to disable form components by just adding 'disabled' attribute to them.

![Cascading Selector](/assets/2012-06-19-jquery-mobile-1.png "css sprites")

The display looks like below:

![Cascading Selector](/assets/2012-06-19-jquery-mobile-2.png "css sprites")

To implement it via Javascript:

```javascript
$('#firstname').prop('disabled', true);  // Disable input
$('#firstname').prop('disabled', '');    // Enable input
```

Or we're able to disable the entire form by adding 'ui-disabled' class to it:

```javascript
$('form').addClass('ui-disabled');
```

Through this way, the labels inside the form are dimmed.

![Cascading Selector](/assets/2012-06-19-jquery-mobile-3.png "css sprites")