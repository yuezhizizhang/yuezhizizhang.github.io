---
layout: post
title:  "What's the use of Array.prototype.slice.call(arguments)?"
categories: javascript
---

```javascript
function makeArray() {
	return Array.prototype.slice.call(arguments);
}
```
makeArray() returns an actual array, which is a copy of arguments. Arguments isn't an real array, even though it has a length property and could be accessed through array indexes. 

To verify that Arguments isn't Array, simply take a look at of Arguments object.  In its prototype chain, there is no Array; hence, it doesn't own slice method.

To convert it into an real array, we resort to Array methods:

[Array().slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice#Array-like_objects)