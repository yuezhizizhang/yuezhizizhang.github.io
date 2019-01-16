---
layout: post
title:  "From Constructor Function to Class"
categories: "javascript"
---

At the era of ECMASCript 5, JavaScript used to be a jungle world, without Namespace, Class, Import, Export etc. A lot of developers from Java, C++ background hate it, thinking the language bizarre. But as long as you get familiar with the aboriginal rules, you will appreciate its simplicity and flexibility. 

### JavaSCript Constructor Function

JavaScript is an object oriented language. Everything in JavaScript is an object except for the primitive values, which include boolean, numbers, string, null and undefined.

A key value map {} is an object itself, an array is an object, a function is an object. Every JavaScript object has a link to a prototype object. And the prototype object has a link to its prototype object. This is called prototype chain. When does the chain end? Well, when null is met. That's where Object sits. Object sits on top of the prototype chain, because its prototype is null. 

Even though object is native to JavaScript, however, object template, namely, **Class** is not. How to define a Class? Via the constructor function. A constructor function is just a function. Following is a simple example of JavaScript constructors and prototypes.

```javascript
function Fruit(name) {
	this.name = name;
}
Fruit.prototype.constructor = Fruit;

function Mango(price) {
	this.price = price;
}
Mango.prototype = new Fruit('Mango');
Mango.prototype.constructor = Mango;
Mango.prototype.getPrice = function(count) {
	return this.price * count;
}

var mango = new Mango(2);
```

Here, we define two Classes, Mango and Fruit. Mango inherits from Fruit. Inheritance is done through prototype. Note that **this** refers to the instance object itself. Let's take a look at the **mango** object in the browser:

<p style="color: navy;">
Mango<br>
&nbsp;&nbsp;&nbsp;&nbsp;price: 2<br>
&nbsp;&nbsp;&nbsp;&nbsp;__proto__: Fruit<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;constructor: f Mango(price)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;getPrice: f (count)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;name: "Mango"<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;__proto__:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;constructor: f Fruit(name)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;__proto__: Object<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;constructor: f Object()<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;....
</p>

The bottom Object doesn't link to any __proto__. That's where the prototype chain ends. We define member functions on the prototype object. The purpose of it is to save memory, because a single function object is shared among all of the Mango object instances.

### Closure and Module Pattern

With JavaScript constructors and prototypes, we are able to define Classes and Inheritance. Nice!

However, have you noticed that all of the Class member variables and functions are public? Actually, anything that is bind to **this** is public. In JavaScript, everything which is not defined within a function is in the public scope, or the Global scope. In a browser, a Global scope is the window. 

So how to create a Private scope? Via **Closure**. And the way to create a Closure is via **function**. I have an old post talking about [Function, Execution Context and Closure](http://yuezhizizhang.github.io/javascript/2012/08/14/function-execution-context-closure.html). 

The easist way to create a closure is through the self-executing anonymous function. This is probably one of the most classic patterns in JavaScript:

```javascript
(function() {
    // It creates a private scope
}());
```

Module pattern is a brilliant usage of closure. Here is how we can define a Class with private members with module pattern.

```javascript
var Mango = function(price, weight) {
	return  mango = {
		price: price,

		getPrice: function(count) {
			return this.price * count;
		},

		getWeight: function() {
			return weight;
		}
	};
}

// To create an instance object
var mango = Mango(2, '1kg');
```
 Note that the property **weight** is private. It's only accessible inside the function closure.

### ECMASCript 6 Class

ES 6 introduces Class to JavaScript. But still it doesn't support private memebers. A simple way to support private members is still through closure.

```javascript
var Mango = function(price, weight) {

	class Mango {
		constructor(price) {
			this.price = price;
		}

		getPrice(count) {
			return this.price * count;
		};

		getWeight() {
			return weight;
		}
	}

	return new Mango(price);
}

// To create an instance object
const mango = Mango(2, '1kg');
```