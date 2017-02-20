---
layout: post
title:  "JavaScript Class with Private Members"
categories: "javascript"
---

Is there such a perfect way to define a JavaScript class? By perfect, I mean namespace, private members, public members, prototype chain are all in one. I couldn't figure out a way in which I have private members while the functions are defined in the prototype chain.
For instance, I want to define a Circle class, which is encapsulated in the namespace Module. It has a private property radius and a public function draw. I want to define draw in the prototype chain. The way I can come up with is something like below:

```javascript
(function(my) {
    var _radius = 0;
    var Circle = function(rd) {
        _radius = rd;
    };
    Circle.prototype.draw = function() {
        console.log('radius: ' + _radius);
    };
    my.Circle = Circle;
}(Module));
```

However, this piece of code is buggy. It's because the private property 'radius' is not belong to an instance, but to a class. If two instances of Circle are created, they share the same radius value.

```javascript
var circle1 = new Module.Circle(1);
circle1.draw();                                      // Output: radius: 1
var circle2 = new Module.Circle(2);
circle2.draw();                                     // Output: radius: 2
circle1.draw();                                     // Output: radius: 2
```

What's going wrong here? The codes neglect the fact that the private variables have to be part of each class instance. Nevertheless, if the private members are defined within the constructor function, then the prototype functions are not able to access them because they are not in a closure. Hence, an alternative way is to define functions on the constructor itself. However, the drawback is each instance has a copy of the function definition, which I don't like.

```javascript
Module.Circle = function(rd) {
    var radius = rd;
    this.draw = function() {
        console.log('radius:' + radius);
    };
};
```

Another alternative is do not try to hide the property. Instead, we try to control the way it's accessed, namely, define our own setter/getter methods. We can achieve that via Object.defineProperty API, which is specified in ECMAScript 5.1.

```javascript
(function(my) {
    var Circle = function(rd) {
        var _radius = 0;
        Object.defineProperty(this, 'radius', {
            enumerable: true,
            get: function() {return _radius;},
            set: function(newValue) {_radius = newValue;}
        });
        this.radius = rd;
    };

    Circle.prototype.draw = function() {
        console.log('radius: ' + this.radius);
    };
    my.Circle = Circle;
}(Module));
```