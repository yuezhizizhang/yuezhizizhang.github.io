---
layout: post
title:  "JSLint: Errors and Solutions"
categories: "javascript"
---

JSLint is cool! It helps us identify a number of hidden bugs in our codes. However, JSLint only represents a type of coding style, which you may not totally agree with. For example, JSLint doesn't expect a function name to start with uppercase; it prefers dot notation to subscript notation. In summary, there are times you would like to suppress JSLint warnings. Following is a guidance of JSLint warnings and possible solutions.

**[E] Missing 'use strict' statement.**<br/>
**[S] "use strict";**

Enable strict mode by placing "use strict"; at the top of a script. To learn more about script mode, please read (http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/)[http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/].

**[E] '$' was used before it was defined.**<br/>
**[S] /*global $: false */**

The JavaScript file is dependent on global variables defined elsewhere. For instance, it depends on the jQuery functions defined in the jquery.js. To tell JSLint that variables used in this file are defined in other files, use /*global */ directive. Separate the global variable names by comma.

**[E] 'window' was used before it was defined.**<br/>
**[S] /*jslint browser: true */**

To use the global properties provided by web browser, such as window and document, add the browser directive.

**[E] A constructor name 'XXX' should start with an uppercase letter.**<br/>
    **Missing 'new'.**<br/>
**[S] /*jslint newcap: true */**<br/>

JSLint enforces that the constructor function names be started with uppercase, while the normal function names be started with lowercase. To ignore this convention, add the newcap directive.

**[E] '[XXX]' is better written in dot notation.**<br/>
**[S] /*jslint sub: true */**<br/>

JSLint prefers dot notation to subscript notation, i.e. user.name is better than user['name']. To allow subscript notation, add the sub directive.

**[E] Expected exactly one space between 'function' and '('.**<br/>
**[S] /*jslint white: true */**<br/>

As far as anonymous function is concerned, JSLint prefers one space between keyword function and the parameter bracket. To mute this warning, add the white directive.

**[E] Move the invocation into the parens that contain the function.**<br/>
**[S] }());**<br/>

End the self-executing anonymous function as (function(){...}()); instead of (function(){...})();.

**[E] Weird assignment.**<br/>
**[S] Convert the subscript notation to dot notation.**<br/>

No specific directive could be used to suppress this warning. My experience is simply converting the subscript notation on the line to dot notation. In this way, the warning is gone.