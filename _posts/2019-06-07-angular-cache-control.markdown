---
layout: post
title:  "Cache-Control is crucial for a single-page web application like Angular"
categories: "Angular6 Cache-Control"
---

Recently, my project is approaching 2.0 release. I met an urgent bug related to browser cache. 

To clarify the task a little bit, my project is an **ASP.NET Core + Angular 6** web application. However, the web server is installed locally on the machine. Hence to visit the web service, user has to visit https://localhost:XXXX. 

The bug occurs while upgrading from 1.0 to 2.0. And It happens on **Chrome** only. Chrome browser always displays the old web pages. Only if you manually reload the page, will the latest web pages be shown.

By inspecting the Network, I found Chrome always load cache from disk, without asking the web server to see if anything has changed.

With the investigation, I gained some in-depth understanding about browser cache and cache control.

**1. Chrome is not sending If-None-Match for localhost**

Even though our HTTP responses have set the ETag header. However Chrome never sends **If-None-Match** request before using the disk cache. Because there are no other cache control related headers, which might override the ETag header, so theoetically, ETag should work. Why does Chrome ignore ETag? On the StackOverflow, somebody mentioned the same problem [chrome is not sending if-none-match](https://stackoverflow.com/questions/22733905/chrome-is-not-sending-if-none-match). It only happens with hostname **localhost**. Not sure why.

**2. Set Cache-Control HTTP response header**

Apparently, ETag alone is not enough. Web server should set appropriate Cache-Control HTTP response headers, **e.g. private, max-age, must-revalidate, no-cache or no-store**. For instance, if the static files becomes stale after a maximum amount of time, then we should set max-age and must-revalidate. It means once the max-age has expired, browser must validate with the server before using the cache.

```c#
applicationBuilder.UseSpaStaticFiles(new StaticFileOptions
{
    OnPrepareResponse = ctx =>
    {
        ctx.Context.Response.Headers.Add("Cache-Control", "max-age=3000, must-revalidate");
    }
});
```

**3. index.html may be cached forever**

For a single-page web application like Angular, **index.html** is like a gate for everything else. In Angular and almost all the other single-page web applications, **routing** happens in the front-end. Basically, by loading index.html, we are loading all the other pages too (If not lazy-load). Hence, If index.html is cached, then every other page is cached as well. We know that Angular production build would generate a hash for every file, but not including the index.html file. Hence, if we are not able to set proper cache control of index.html, it might be cached forever. Actually, in our project, because we didn't set proper cache control, index.html is cached by Chrome. Hence, all of the other pages are cached too.

Supposing if the web server doesn't set proper cache control HTTP response headers, then it's front-end's responsibility to set proper HTTP request headers. By adding the &lt;meta&gt; tag in the head of html, we could do it. The following example says no-cache and re-validate with server before using disk cache.

```html
<meta http-equiv="Cache-Control" content="max-age=0, must-revalidate"/>
<meta http-equiv="Pragma" content="no-cache"/>
<meta http-equiv="Expires" content="0" />
```

**4. Use HTTP Interceptor to disable caching of data**

To disable the caching of AJAX data, we can use HTTP Interceptor in Angular. [Disabling Cache in Angular 5](https://medium.com/@tltjr/disabling-internet-explorer-caching-in-your-angular-5-application-3e148f4437ad).

```typescript
intercept(req: HttpRequest<any>, next: HttpHandler):
  Observable<HttpSentEvent | HttpHeaderResponse | HttpProgressEvent | HttpResponse<any> | HttpUserEvent<any>> {
  const nextReq = req.clone({
      headers: req.headers.set('Cache-Control', 'no-cache, no-store, must-revalidate')
      .set('Pragma', 'no-cache')
      .set('Expires', 'Sat, 01 Jan 2000 00:00:00 GMT')
      .set('If-Modified-Since', '0')
  });

  return next.handle(nextReq);
}
```

The above method is to set HTTP request caching header.

**5. Set ResponseCache in ASP.NET**

As well, we can set HTTP response caching header in ASP.NET. [Response caching in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/response?view=aspnetcore-2.2).