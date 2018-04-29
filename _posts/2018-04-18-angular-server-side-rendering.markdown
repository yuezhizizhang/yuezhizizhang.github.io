---
layout: post
title:  "Server Side Rendering Splash page in Angular 5 + ASP.NET Core"
categories: "angular5 server-side-rendering asp.net"
---

In my last post [Clarity + Angular 5 with ASP.NET Core](http://yuezhizizhang.github.io/angular5/clarity/asp.net/vs2017/2018/03/16/clarity-angular5-aspnet.html), I have demonstrated how to create an Angular ASP.NET Core project in Visual Studio 2017 and how to upgrade Angular from 4 to 5. In this post, I will talk about how to utilize Angular Server Side rendering to implement your splash page.

Angular server side rendering is a new feature. In the beginning, I don't know its existence. Hence, I met a bug in my project. That is the landing page is wrong. In my router below, I have specified the landing page to be the SplashComponent. However, at loading the Angular app, it always displays the ConnectComponent firstly.

```typescript
export const ROUTES: Routes = [
    { path: '', redirectTo: '/home', pathMatch: 'full'},
    { path: 'home', component: SplashComponent },
    { path: 'connect', component: ConnectComponent },
    ...
];
```

By printing out the router's trace log, what's more weird is the ConnectComponent is shown even before the router visiting '/'. It looks a lot like server side routing, isn't it?

To learn more about Angular Server Side rendering, you can read [Angular Universal and Server Side Rendering (Step By Step)](https://malcoded.com/posts/angular-fundamentals-universal-server-side-rendering). In short, with Angular Server Side rendering, you are enabled to display a HTML landing page before Angular is fully downloaded and bootstraped.

Let's go back to the Visual Studio Angular with ASP.NET Core project. In our project, there are three modules, namely **app.browser.module.ts**, **app.server.module.ts** and **app.shared.module.ts**. Both the browser and the server's AppModule have imported the shared AppModuleShared. I want to say it is totally wrong. Once the Angular is bootstrapped, the browser AppModule will take over and the server AppModule is discarded. Hence, there is no need to import so many stuff into the server's AppModule.

What's really needed in the server's AppModule is a server AppComponent which acts as the landing page of the app.

```typescript
import { AppServerComponent } from './components/app-server/app-server.component';

@NgModule({
    bootstrap: [ AppServerComponent ],
    declarations: [
        AppServerComponent
    ],
    imports: [
        ServerModule
    ]
})
export class AppModule {
}
``` 

This AppServerComponent could be as simple as a page with a background and a big logo in the middle. 