---
layout: post
title:  "Asynchronous Calls From Callback To Promise To Async/Await"
categories: "javascript asynchronous"
---

There are already plenty of awesome tutorials about **Callback** -> **Promise** -> **Async/Await** on the web. However, I still decide to write something about it, because it's demonstrating the evolution of the web development. My experience with the three makes me feel I have witnessed the history. So this blog is not a tutorial, but groans about the past.

### Ajax Callbacks

Back in 2011, Ajax was blowing the world. During that time, if you Google JavaScript programming, everybody talked about Ajax, Asynchronous, and Web application. Ajax is so fundamental because it enables the browser to exchanges chunks of data with the server without refreshing the whole page. From then on, front end has been taking more and more responsibilties from the server. Web application becomes a popular topic. I still remember my interview with Amazon on 2012. After 3 to 4 rounds of brain wrestling with Algorithm, I finally went to the on-site interview. The first question from my interviewer is "Do you know Ajax"? Isn't it a bit sarcastic? BTW, back in 2012, Amazon's stock price was only $200. But it has zoomed ever since, to more than <span style="color: red">***$2000***</span> right now. What an **idiot** am I rejected Amazon offer??

Back to asynchronous callback. Ajax is asynchronous of course. At that time, I haven't heard about Angular yet. The hottest libraries were jQuery, Bootstrap, jQuery UI and jQuery Mobile. It was basically everything jQuery. In jQuery, an Ajax callback looks like below:

```javascript
function getJSON(url, callback) {
	$.ajax({
	    type:"GET",
	    url: url,
	    dataType: "json",
	    success: function(data){
	        callback(data);
	    }
	});
}
```

success is an Ajax success callback function. Inside it, you probably would call another method in the closure to deal with the data.

### Promise

When the year goes to 2013, the exploding topics of Web become HTML5, ECMAScript 6 and AngularJS. I attended the HTML5 conference in San Francisco on that year. After I returned, I refactored my project to use AngularJS. Promise is a new feature of ECMAScript 6. What does Promise do? Well, it avoids the callback hell, blabla.... With Promise, an Ajax call looks like:

```javascript
ajax({
    type:"GET",
    url: url,
    dataType: "json"
})
.then(function(data) {
	// Step 1
})
.then(function(result) {
	// Step 2
})
.catch(function(err) {
	// Error handling
});
```

Asychronous callback is sort of like a matryoshka doll set. The dolls are nested one inside another. However, Promise is more like laying out the stuff side by side on the timeline. You process one, then proceeding to the next one.

### Async/Await

I'm not sure if the idea of Async/Await is borrowed from C#. In C# language, I firstly encountered this pattern. Async/Await is a part of ECMAScript 2017. Not a surprise, IE11 doesn't support it. But Aysnc/Await is so simple. Logically, you are converting things from asynchronous to synchronous. The basic idea of Async/Await is if one action takes an amount of time, it's alright, I will wait. Aysnc/Await could be wrapped in a for loop. Isn't it amazing?

```javascript
for ( let i = 0; i < max; i++ ) {
	let result = await ajax({
	    type:"GET",
	    url: url + "i",
	    dataType: "json"
	});

	// deal with result
	...
}
```

### Historic Readings

This section is a tribute to all of these awesome documents I've read through the years. The first two "<Learning JavaScript Design Patterns>" and "<Dive into HTML5>" are in particular. They are like bibles. Every web developer must read them.

[Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)

[Dive into HTML5](http://diveinto.html5doctor.com/)

[ECMAScript 6](http://es6-features.org)

[HTML5 Boilerplate](https://html5boilerplate.com/)
