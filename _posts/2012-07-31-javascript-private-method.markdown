---
layout: post
title:  "JavaScript Private Methods"
categories: javascript
---

People share some consensus on what's the best way in defining a public method for a JavaScript Class, that is:

```javascript
classname.prototype.methodname = function() {...}
```

The biggest advantage of defining methods via prototype rather than straight inside the constructor is that a single function object is shared among all the class instances. 

By contrast, there is no such rule of thumb on how to define private methods. A simple and probably most prevalent way is declaring the private functions using var inside constructor, for example:

```javascript
function TestClass() {
	
    var privateMethod = function() {
        alert('A private method is invoked');
    };

    this.publicMethod = function() {
        privateMethod();
    };

}
```

However, a major drawback of this solution is that the public methods which make use of the private methods must be defined in the same scope as that of private methods. That is these public methods have to be defined inside constructor too. As a result, each class instance has a respective copy of these function objects. 

How to avoid the redundancy? Self-executing anonymous function comes to help.

```javascript
(function() {
    // Bunch of codes
}());
```

Self-executing anonymous function creates a function-level scope, which is a perfect place to wrap private methods:

```javascript
(function() {
    function TestClass() {}

    var privateMethod = function() {
        alert('A private method is invoked');
    };

    TestClass.prototype.publicMethod = function() {
        privateMethod();
    };
}());
```

See it! By defining privateMethod inside the self-executing anonymous function, we are able to isolate it from global scope. That is privateMethod is not accessible outside the function. Yet, it's exposed to publicMethod because publicMethod is defined in the same function-level scope. Additionally, publicMethod is defined via prototype which ensures that all the instances of TestClass share a single function object.