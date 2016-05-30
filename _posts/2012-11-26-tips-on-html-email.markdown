---
layout: post
title:  "Tips on Creating and Sending a HTML Email"
categories: javascript
---

* How to send a HTML Email with Embedded images via Outlook?

The simplest way I've found is: First, open the HTML with IE browser. Subsequently, Ctrl+A to select the whole page and Ctrl+C to copy it. Finally, create a new mail message and use Ctrl+V to paste the HTML into the mail. In this way, the images are embedded into the mail message.

* What's the usual size of a HTML Email?

600 to 650 pixels in width.

* How to create a HTML Email?

**Rule 1 - Use Table for Layout**

A HTML email is a compound of nested tables. Do not risky using Div for layout. It may break your heart in the end.

**Rule 2 - Use inline styles**

Use inline styles. Do not use the &lt;style&gt; declaration in the HTML head.

**Rule 3 - Avoid using background image**

It proves that Outlook has problems dealing with background image. Hence, try to avoid using it. Instead, use <img> if needed.

**Rule 4 - Pixel by pixel work**

Bear in your heart that that it would be a pixel-by-pixel tedious work right when you start designing it. Protect your eyes!

* Professional Guidance

[http://www.sitepoint.com/code-html-email-newsletters/](http://www.sitepoint.com/code-html-email-newsletters/)

[http://www.sitepoint.com/principles-beautiful-html-email/](http://www.sitepoint.com/principles-beautiful-html-email/)