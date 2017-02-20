---
layout: post
title:  "Ctrl+Shift+R for Chrome"
categories: "cache"
---

Chrome caches like a mad dog. It is really annoying for a web developer.

```
F5 or Ctrl+R => Reload the current page.
```
F5 doesn't save your ass. F5 reloads the page, however, cache is still used.

To reload a page ignoring cached contents, you should use:

```
Ctrl+Shift+R or Ctrl+F5 or Shift+F5 => Reload the current page, ignoring cache.
```
It forces the browser to re-download everything, css/javascript files, images, etc.