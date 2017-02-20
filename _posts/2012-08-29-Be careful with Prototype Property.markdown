---
layout: post
title:  "Be Careful with Prototype Property"
categories: "javascript"
---

Today, I have spent nearly half a day wrestling with a bug. Eventually, it turned out that the root cause is the misuse of prototype property. Prototype property is like a pitfall for someone like me who comes from the object-oriented world. In Java, for instance, it's quite normal to define a common property in the base class, so that all the derived classes contain this property. Hence, it's no surprise I wrote down some silly codes like below:

```javascript
function BaseModel() {
    this.map = {};
}

function ChildModel() {

}

ChildModel.prototype = new BaseModel();
ChildModel.constructor = ChildModel;

ChildModel.prototype.insert = function(key, value) {
    this.map[key] = value;
};
ChildModel.prototype.teardown = function() {
    this.map = {};
};

var child = new ChildModel();
child.insert('salary', '2000');
child.teardown();

child = new ChildModel();
console.log(child.map);
```

Everything looks fine! ChildModel is a constructor function. Its prototype is a BaseModel object, which owns a property map. In the beginning, an instance of ChildModel is created and assigned to child. It inserts a key-value pair 'salary : 2000' into map. Subsequently, it restores map to an empty object. And then, another instance of ChildModel is created and assigned to child. I assumed that the map property of this newly created instance is clean. Isn't it?

![Cascading Selector](/assets/2012-08-29-prototype-property-1.png "prototype")

Oh, no. It still holds the key-value pair 'salary : 2000'. Haven't I already cleaned it? The pity is I didn't. Let's take a closer look at insert and teardown function and find out why. In the insert function, child manipulates the prototype property map directly. Hence, the key-value pair 'salary : 2000' is stored in the prototype property map.

![Cascading Selector](/assets/2012-08-29-prototype-property-2.png "prototype")

However, in the teardown function, child restores map to a clean object. Doesn't it? What, it doesn't touch the prototype property map, instead, it creates a new property map on itself. And the newly added property overrides the prototype property.

![Cascading Selector](/assets/2012-08-29-prototype-property-3.png "prototype")

Oh, I see. So the second created instance of ChildModel still inherits from prototype object. And the prototype object still contains the key-value pair 'salary : 2000'.

![Cascading Selector](/assets/2012-08-29-prototype-property-4.png "prototype")