---
layout: post
title:  "Learn jQuery source codes with Paul Irish"
categories: "javascript jquery"
---

Here is a video by Paul Irish explaining some details about jQuery source codes. Personally, I was amused while watching this video. I particularly like all of the unexpected laughs throughout the presentation. Haa....

[10 Things I Learned from the jQuery Source](http://www.paulirish.com/2010/10-things-i-learned-from-the-jquery-source/)

Following are some tips of JavaScript programming given by Paul.

**1. Self-executing Anonymous Function**

```javascript
(function() {
    // ...
}());
```

What's particular of this function is: Firstly, it's anonymous. In other words, no name refers to this function; hence, it can't be invoked anywhere else. Secondly, this function is immediately called at declaration. In addition to the above standard way, following two methods could also be used to create self-invoking anonymous functions.

```javascript
!function() {
    // ...
}();

+function() {
    // ...
}();
```

What's the point of the JavaScript Pattern below?

```javascript
(function(window, document, undefined) {
    // ...
}(this, document));
```

Well, the window and window.document object are passed into the function as parameters. Obviously, it gives us sort of scope traversal benefit because inside the function we don't have to refer to outer scope (global scope) to access window and document object. The window and document object are now within the function's local scope. In addition, we are able to guarantee that undefined is actually of undefined value since no value is passed in here.

**2. An Elegant Substitute for setInterval**

Many have argued that setInterval is an evil function. It keeps calling a function at specified intervals regardless of whether the function is finished or not. For example, it's quite likely that function doStuff() takes more than 100 milliseconds to complete. However, setInterval doesn't care about it and it keeps invoking doStuff() every 100 milliseconds.

```javascript
setInterval(function() {
    doStuff();
}, 100);
```

So, an alternative to setInterval is as below. The following function invokes itself every 100 milliseconds as well. But each time it runs, it guarantees doStuff() is actually finished before next calling is registered. And another huge benefit to developers is that we're able to debug doStuff() now. The debug won't be interrupted by another function calling.

```javascript
(function() {
    doStuff();
    setTimeout( arguments.callee, 100 );
}());
```

**3. propFix**

propFix is simply an object containing some name-value pairs. It magically does some translations so that you are able to access, say, a class attribute via "jQuery('#id').attr('class')". On a DOM element, you know, class attribute is actually named className, because class is a reserved word. With the help of propFix, you don't have to worry about these spelling issues anymore. What's more, you are able to add some default properties in propFix so that you can use them natively.

![propFix](/assets/2012-03-02-variable-propfix.png "propFix")

**4. CSS Selectors**

$('#id').find('li') works faster than $('#id li'). Why? If you debug jQuery codes, you could see that the later one invokes Sizzle CSS Selector Engine whereas the former one doesn't.

**5. Parse JSON**

All of the popular browsers have built-in JSON parsers. Of course, jQuery attempts to parse using native JSON parser first.

```javascript
// Attemp to parse using the native JSON parser first
if ( window.JSON && window.JSON.parse ) {
    return window.JSON.parse( data );
}
```

If there is no native JSON parser, jQuery resorts to using Function's return method. This mechanism is almost the same as using eval() function. Both of ways turn a string into an object and both of them couldn't avoid executing some malicious codes.

```javascript
return ( new Function( "return " + data ) )();
```

**6. Latest jQuery Source Code**

[jQuery 1.11.1](http://bit.ly/jqsource)