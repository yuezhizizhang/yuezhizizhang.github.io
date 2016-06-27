---
layout: post
title:  "Function, Execution Context and Closure"
categories: javascript
---

I used to assume that I knew closure. Not until I read this great article http://dmitrysoshnikov.com/ecmascript/javascript-the-core/, I realized I was totally wrong.

Actually I had some suspicions long ago, however, I didn't dig any deeper. My suspicion arose from the code snippet below:

```javascript
function func(key) {
    
    function print() {
        console.log(key + ' timeout callback invoked.');
    }

    // Register timeout callback function.
    window.setTimeout(print, 3000);
    // Print out the end of execution.
    console.log(key + ' execution ends.');
}

func('first');
func('second');
```

It's a simple piece of codes. func prints out the parameter key after a 3-seconds delay. However, the execution result doesn't seem simple.

![Cascading Selector](/assets/2012-08-14-closure-1.png "closure")

The timeout callback function print magically remembers the respective argument of each call. HOW? Obviously, each timeout callback function is invoked long after both func executions end. And there is only one func object in the global scope. Therefore, intuitively, if the parameter key is a member of func object, both callback functions should print out the same message 'second timeout callback invoked.'. Whereas the fact is the first callback function actually prints out the argument passed in by the first invocation of func, that is 'first'. The conclusion is there must be respective objects storing the information of each execution of a function.

* **Execution Context**

My deduction proves right. There is such a thing called Execution Context. Each call of a function generates a new execution context. Every execution context contains three elements: Activation Object, Scope Chain and This value.  Activation Object is where arguments, local variables and local function declarations are stored. Takes the code snippet above as an example, the  Activation Object of each calling of function func looks like below:

**func('first')**

<table border="1" cellspacing="0" cellpadding="5">
<tr>
    <th colspan="2" bgcolor="#F5DEB3">Activation Object</th>
</tr>
<tr>
    <td>key</td>
    <td>first</td>
</tr>
<tr>
    <td>arguments</td>
    <td>{0:"first",…}</td>
</tr>
<tr>
    <td>print</td>
    <td>&lt;function&gt;</td>
</tr>
</table>


**func('second')**

<table border="1" cellspacing="0" cellpadding="5">
<tr>
    <th colspan="2" bgcolor="#F5DEB3">Activation Object</th>
</tr>
<tr>
    <td>key</td>
    <td>second</td>
</tr>
<tr>
    <td>arguments</td>
    <td>{0:"second",…}</td>
</tr>
<tr>
    <td>print</td>
    <td>&lt;function&gt;</td>
</tr>
</table>

key is the parameter passed in. arguments object is a map of parameters passed in with index keys plus a few more properties such as length and callee. print is the callback function declaration. So, how does the print  function access the parameter key long after func execution ends? Apparently, via Closure. But how does closure work? Well, at the moment print is declared or so-called created, it saves its parent's Scope Chain. And when print is really invoked, it combines the parent's Scope Chain and the  Activation Object of its own together and forms its own Scope Chain. 

![Cascading Selector](/assets/2012-08-14-closure-2.png "closure")

When resolving key, it traverse the scope chain from print AO up to the top of the scope chain. In the example,  key is found in the func AO. See, the essence of closure is scope chain. One thing to highlight is that the parent's Scope Chain is saved at creation time, not at execution time. The following example demonstrates it.

```javascript
var x = "global scope";

function foo() {
    console.log('x is defined in the ' + x);
}

(function bar(funcArg) {
    var x = "function scope";
    funcArg();
}());
```

function foo is passed into to function bar as a parameter. At invoking foo, whether x defined in the global scope or x defined in the function scope is reference? The fact is that the global x is accessed. 

![Cascading Selector](/assets/2012-08-14-closure-3.png "closure")

Parent's Scope Chain is saved at the declaration or creation of function foo, rather than at execution time. Hence, the scope chain of foo looks like below:

![Cascading Selector](/assets/2012-08-14-closure-4.png "closure")

In addition, because inner function declaration is stored in the Activation Object and each execution creates a new AO, it's not surprisingly that in the following example, func1 and func2 are totally different objects. 

```javascript
function create(price) {
    function print() {
        console.log("Price is " + price);
    };

    return print;
}

var func1 = create(20);
var func2 = create(40);
console.log('func1 === func2 : ' + (func1 === func2));
```

![Cascading Selector](/assets/2012-08-14-closure-5.png "closure")