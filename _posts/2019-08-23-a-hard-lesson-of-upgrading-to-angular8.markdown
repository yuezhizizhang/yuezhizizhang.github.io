---
layout: post
title:  "A hard lesson of upgrading to Angular 8"
categories: "Angular8 Clarity2"
---

Back from a super hot vacation in the northern hemisphere, my first task was to upgrade our app from Angular 6 + Clarity 0.13 to Angular 8 + Clarity 2. It turns out to be a super hard nut to crack. 

**Firstly**, a remind for anyone using [Clarity](https://clarity.design/documentation), there is a breaking change between Clarity 0.13 and Clarity 1 and above. The HTML forms are affected the most. The class names, even the layout of the form is totally different. As a result, I have to rewrite almost all of the forms in our app to support Clarity 2.

**Secondly**, the performance of the app has deteriorated a lot after upgarding to Angular 8. By poor performance, I mean the same AJAX call takes much longer time. Consequently, to load a datagrid, you would see the spinner going round and round before the data finally comes up. 

**Finally**, our app with Angular 8 doesn't work on IE11 and Edge anymore. 

Regrading the 3 problems above, the Clarity upgrade, even though it's a tedious job, but is the simplest one to solve. However, the performance issue and the IE11/Edge support are not so easy to figure out why. As I dig further, there are a variety of reasons behind it.

### 1. Upgrading from Angular 6 to Angular 8, IE 11 doesn't work anymore.

Since Angular 8, the TypeScript compiler is targeting at a newer ECMAScript version **es2015** by default. Hence, when you upgarde Angular, in the tsconfig.json file, the target version is updated to **es2015** as well. As we know, IE 11 and legacy browsers don't support **es2015**. Consequently, IE 11 can't load the app anymore. To support IE 11, you have to change the compile target back to **es5**.

> **tsconfig.json**
```json
"compilerOptions": {
    "target": "es5",  // When upgarding to Angular 8, it is updated to 'es2015' by default.
    ...
}
```

Actually, by downgrading the TypeScript compile version back to **es5**, we have disabled a new Angular 8 feature called **differential loading**.

What is **differential loading**? These two blogs [A Guide to Angular 8's Differential Loading](https://auth0.com/blog/angular-8-differential-loading/) and ["Differential Loading" - A New Feature of Angular CLI v8](https://dev.to/lacolaco/differential-loading-a-new-feature-of-angular-cli-v8-4jl) have a detailed explanation. In simple words, Angular 8 generates two bundles of JavaScript files out of the box. One set is targeting at modern browsers, with a file name ending like **-es2015.js** if your compile target is **es2015**. If say your TypeScript compile target is updated to **es2016**, then the file names end like **-es2016.js**. The other set of JavaScript files are targeting at legacy browsers, e.g. IE11. The file names all end like **-es5.js**. These two sets of JavaScript files are all linked in the index.html. Modern browsers have the ability to interpret a module type in the script HTML tag and to ignore a nomodule attribute. Likewise, when an older browser sees this, it won’t recognize the module type and only load -es5.js.

```html
<script src="main-es2015.js" type="module"></script>
<script src="main-es5.js" nomodule>
```

To generate these two bundles of JavaScript files correctly, you have to configure a file called **browserslist**. It specifies which browsers are supported. By default, IE9-IE11 are not supported.

> **browserslist**
```
not IE 9-11 # For IE 9-11 support, remove 'not'.
```

### 2. The Chrome browser launched by Visual Studio is 10 times slower than the normal Chrome.

I did a performance profiling in Chrome. The first issue caught my eye is of the same AJAX call, Chrome browser spawn by Visual Studio takes 10 times longer time than a normal Chrome. 

It turns out 'Enable JavaScript debugging for ASP.NET' would make Chrome launched by Visual Studio super slow. Since JavaScript debugging is not really working with Chrome in Visual Studio 2017, I have disabled this option without a thought:

> **Disable JavaScript debugging for ASP.NET**
```
1. Go to Tools > Options
2. Go down to the Debugging > General option on the left side of the menu.
3. Find and uncheck the Enable Javascript debugging for ASP.NET(Chrome and IE).
```

### 3. AJAX call in Angular 8 is considerably slower than Angular 6

Haha, this is probably the most tricky problem. Why is the same AJAX call takes longer time in Angular 8 than Angular 6? I'm using HttpClient, which is recommended by Angular. Then what's going wrong?

Well, the real problem is with **Promise** and **async/await**. In our app, every HTTP request is turned into a **Promise**. In addition, all of the asynchronous calls are turned into synchronous looking codes with **async/await**.

You remember to support IE 11, I have changed the TypeScript compile target back to **es5**. Apparaently, the generated JavaScript codes has a poor performance. This blog [Compiling async/await to ES3/ES5 in TypeScript](https://mariusschulz.com/blog/compiling-async-await-to-es3-es5-in-typescript) explains how awful JavaScript codes would be if compile **async/await** to **es5**. Having said that, however with Angular 6, even though TypeScripts are compiled to **es5** as well, I didn't feel **async/await** has a such big impact on the performance.

Anyway, to improve the AJAX performance, we have to drop support of IE 11 and compile TypeScripts to **es2015** instead. Another reason we can't support IE 11 anymore is because Clarity 2 datagrid is almost not responding in IE 11. When you drag the scrollbar, the page refreshes very slowly.

### 4. Upgrading TypeScript compile target to es2015, Edge doesn't work anymore.

Edge used to work when compiling TypeScripts to **es5**, but not working anymore when upgrading to **es2015**. It's a more serious problem than not supporting IE 11 or poor performance. In the Edge web console, no errors are logged. However, by opening the Network tab, I saw Edge is firing endless AJAX calls in one of the page. What's special about this AJAX call? Well, it's invoked in an **override async method**.

```typescript
/** This is overriding the parent getPage method **/
protected async getPage(): Promise<void> {
    super.getPage();
    this.getState();
}

/** This method is called infinitely in Edge browser **/
protected async getState(): Promise<void> {
    // AJAX calls
}
```

**Do not override async methods in typescript!** It fails Edge browser.

### 5. Angular 8 builds super slow

We know that to support **differential loading**, Angular 8 would build two bundles of JavaScript files, one set to support modern browers, the other set to support legacy browsers. Hence, it takes at least twice the build time as before.

A bad thing is even if you don't care about legacy **es5** browsers, you can't turn off **-es5.js** building. In the **browserslist** file, I have configured 'not IE 9-11', however, **-es5.js** files are still generated. Frustrating!

Due to the slow build time, when you run 'ng serve', Chrome browser will timeout from time to time at any code changes. Even at launching the app, Chrome tab would timeout and you have to open a new tab to open the app. This thread is talking about it: https://github.com/angular/angular-cli/issues/14666. 

Alright, these are my diggings. Hope it will help you when you have to upgarde Angular to 8.