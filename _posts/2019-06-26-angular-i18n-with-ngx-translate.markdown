---
layout: post
title:  "Angular i18n + ngx-translate"
categories: "Angular6 Localization Internationalization"
---

> All the struggles and downfalls will pay off.

### Angular i18n AOT build

My struggle with Angular 6 internationalization and localization library **i18n** is it doesn't support **JIT** translation for production build. If you take a closer look at Angular AOT build, you can see for each language you have to run a build:

```cmd
ng build --prod --i18n-file src/locale/messages.fr.xlf --i18n-format xlf --i18n-locale fr
``` 

In AOT build, the locale strings are built into the template. So how many languages you have to support, how many applications you have to build.

Hence, to switch languages, it is not as simple as loading another locale json file and use it. Instead, you have to reload the entire application from another locale directory. 

To learn more about the limitations of i18n, you may be interested in reading [Translation not recognized on production](https://github.com/angular/angular/issues/16339) and [Why ngx-translate exists if we already have built-in Angular2+ i18n](https://github.com/ngx-translate/core/issues/495).

Angular documentation talks a lot about why they don't support JIT translation in the AOT build, namely, to boost performance. But sometimes, the launch performance is not that really emphasized. Then what about the build performance, like in this discussion thread [Faster / parellel i18n AOT builds](https://github.com/angular/angular-cli/issues/6789).

Anyway, my project switches to use **ngx-translate** library eventually.

### Angular i18n vs. ngx-translate

Another big limitation of Angular i18n is it doesn't provide means to use translation strings in the typescript. Please read the discussion thread about it [i18n: Able to use translation strings outside a template](https://github.com/angular/angular/issues/11405).

Even though there are so many drawbacks of i18n, however, I truly love the way i18n marks the templates, so easy and simple, just by adding an **i18n** attribute to the translatable content.

```html
<button i18n="@@saveBtn">
    SAVE
</button>
```

The powerful Angular i18n extractor **ng-xi18n** will help you extract the locale strings to an xliff2 file. Thus, you don't have to maintain the resource file manually.

```json
"i18n:extract": "./node_modules/.bin/ng-xi18n --i18nFormat=xlf2 --outFile=./assets/i18n/messages.xlf"
```

By comparison, **ngx-translate-extract** sucks, because it only extracts the key, but not the texts. You have to fill in the text string in the json file manually. What ngx-translate-extract is generating is like:

```json
{
    "saveBtn": ""
}
```

### ng-xi18n + ngx-translate

How to utilize the power of ng-xi18n extractor but still using ngx-translate library to do translation?

The solution is:

**1. mark the template with both i18n attribute and translate attribute**

```html
<button i18n="@@saveBtn"
	    [translate]="'saveBtn'">
    SAVE
</button>
```

**2. Extract the locale strings with ng-xi18n**

```json
"i18n:extract": "./node_modules/.bin/ng-xi18n --i18nFormat=xlf2 --outFile=./assets/i18n/messages.xlf"
```

**3. Conver the xlf file to json file.**

In the meantime, trim the leading and trailing whitespaces from the text strings, which ngx-translate-extract doesn't do for you. And sort the keys by alphabetical order so that next time when something is added, not the whole file is changed.

**4. Load and use the local json file with ngx-translate.**

There are some more tricks with this combination method.

For example, to extract and translate a title properly:

```html
<button id="save-btn"
        i18n-title="@@saveBtn"
        title="Save"
        [title]="'saveBtn' | translate">
</button>
```

Finally, I'm sorry I can't provide the node scripts which converts the xlf file to json file. It's all VMware owned.