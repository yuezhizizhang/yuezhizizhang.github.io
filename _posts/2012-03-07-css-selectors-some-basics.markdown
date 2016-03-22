---
layout: post
title:  "CSS Selectors (Some Basics)"
categories: css
---

CSS Selectors are nice, fancy, awesome... blablabla....Yep, I know that feeling. But you know there is no perfect world; hence, power usually comes along with cost. That's why we have to dig into the basics of CSS selectors to ensure that we don't misuse it at the expense of the rendering performance of browsers.

**1. Right to Left**

Browsers parse CSS selectors from right to left. For example, for CSS selector .footer h3 {...}, the browser starts from the rightmost tag selector h3, finding out all the h3 elements. And then the browser traverses up the DOM tree, evaluating every ancestor element to see if it's of class footer until it reaches the root element. Bearing the rule 'Right to Left' in mind, it's easy for us to understand why following selectors are inefficient:

```css
form#submit {...} /* Overly qualified selectors -
					ID selector is unique. Including tag selector adds redundant
					information that needs to be evaluated. */

body ul li a {...} /* Descendant selectors are inefficient.
					body selector is redundant because all elements are 
					descendants of body. */

.number * {...} /* Universal selector is the least efficient one. */
```

**2. Cascading Order**

Cascading Style Sheets. What's the actual meaning of cascading? The so-called cascade refers to the priority or weight scheme to determine which style rules apply if more than one rule matches against a particular element. The rules concerning the CSS selectors are chosen in the following order:

* the rules with more ids in the selector
* the rules with more classes in the selector
* the rules with more tags in the selector

In the example below, all the tag selector, the class selector and the ID selector are targeting the div element. However, only the ID selector applies because ID selector weights more than class selector, while class selector weights more than tag selectors.

```html
<div id="main" class="red">
	<ol>
		<li>Arctic</li>
		<li>Atlantic</li>
		<li>Pacific</li>
		<li>Indian</li>
		<li>Southern</li>
	</ol>
</div>
```
```css
div {
	border: none;
}
.red {
	border: 2px solid dashed #f00;
}
#main {
	border: 5px solid teal;
}
```

![Cascading Selector](/assets/2012-03-07-css-selector-1.png "id selector")

The specificity is calculated by counting the respective occurs of ID attributes, class attributes and tag attributes in the selector. And then write the three numbers in exact order with no spaces or commas to obtain a three digit number. The selector of larger number is applied. See, #main wins in the compete. 

```
div  /* a = 0, b = 0, c = 1 --> specificity = 001 */
.red /* a = 0, b = 1, c = 0 --> specificity = 010 */
#main /* a = 1, b = 0, c = 0 --> specificity = 100 */
```