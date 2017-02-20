---
layout: post
title:  "Tips of jQuery Mobile"
categories: "jquery jquery-mobile"
---

I have been intensively working with jQuery Mobile during recent days. Following are some of the tips I grasp via using this library.

### Methods

**1. $.mobile.changePage**

It enables programmatically change from one page to another. That implies your Javascript codes take full control of page loading and page transition. For example, after the user correctly fills and submits the authentication form, the view changes from the login page to the home page, with a sliding left transition effect.

```javascript
$.mobile.changePage('home.html', {
    transition: "slide"
});
```

The first parameter is a string which specifies the relative URL of the home page resource. The second parameter is an object with one property 'transition', which specifies the transition to use when showing the page. Actually, you could specify more properties in the second parameter. Please refer to http://jquerymobile.com/test/docs/api/methods.html for the details.

By taking a closer look of jQuery Mobile codes, we could find out what has been done after invoking the method above. Firstly, 'pagebeforechange' event is triggered prior to any page loading and transition. Then, $.mobile.loadPage is invoked to load external page 'home.html' via Ajax. The Ajax call looks like below.

```javascript
$.ajax({
    url: fileUrl,
    type: settings.type,
    data: settings.data,
    dataType: "html",
    success: function( html, textStatus, xhr) {
    ...
```

By default, Ajax request type is 'GET' and data is 'undefined'. All these default configurations could be rewritten via the second parameter of  $.mobile.changePage method. In addition, cross-domain Ajax request is disallowed by default. To allow cross-domain requests, $.mobile.allowCrossDomainPages configuration option should be set to true. After the external page is successfully loaded and injected into DOM, a 'pageload' event is triggered. Later on, page transition occurs. Finally, after the page transition animations complete, 'pagechange' event is triggered. In addition to above events, a 'hashchange' event is also triggered because the current location hash is changed.

**2. $.mobile.path.parseUrl**

URL path helper provide many useful utility methods. One of them is parseUrl function. It parses a URL into a structure that allows easy access to parts of the URL. It returns an object which allows us to access the sub-parts of the URL.

![Cascading Selector](/assets/2012-06-04-jquery-mobile-1.png "jquery mobile")

Additionally, path object also provides functions to get (get()) and set (set(path)) location hash, to check whether a URL is relative (isRelativeUrl(url)) or absolute (isAbsoluteUrl(url)).

### Refresh

Refresh is highly important especially when the page is partially updated. For example, if we update a select via Javascript, we have to call the refresh method on it to update the visual styling.

```javascript
var select = $('#domain-select');
select.append('<option value="GOOGLE">Google</option>');
select.selectmenu('refresh');
```

Here, we add options into select dynamically via Javascript. Afterwards, we have to invoke refresh explicitly so that the display could be refreshed. It's the same case with list views. If we add items to a listview, we have to call .listview('refresh') on it.

Another generic way to refresh the display is to trigger 'create' event.  In most of the cases, it's the easiest way to update the whole display.

```javascript
$page.trigger('create');
```