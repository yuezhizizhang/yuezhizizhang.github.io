---
layout: post
title:  "Angular NgModule"
categories: "Angular 6"
---

Every Angular application bootstraps with a NgModule. It justifies how important module means for an Angular app.

### What does NgModule do?

In my understanding, NgModule has two main roles, one is Dependency Injection, the other is Namespace.

### NgModule is NOT ES6 Module

ECMAScript 6 module is all about import, export and resolving these circular dependencies. Whereas Angular NgModule is about the application itself. It's about how to organize an application, how to make use of external libraies, and what to publish (components or services) so that other modules can access them.

### Dependency Injection and IoC

NgModule is like a **Service Container**. Components and services are registered in it, so that they can be dependency injected into where they are used. NgModule declares components, directives and pipes.

```typescript
@NgModule({
  declarations: [
    SomeComponent,
    SomeDirective,
    SomePipe
  ]
})
```

It makes some of them public by exports them.

```typescript
@NgModule({
  exports: [
    SomeComponent
  ] 
})
```

But service provider is a somewhat different story. Before Angular 6, the only way to provide services is by declaring them in the providers of NgModule.

```typescript
@NgModule({
  providers: [
    SingletonService1,
    SingletonService2
  ] 
})
```
Services declared this way are **public** and **singleton** throughout the app. It's a bit annoying because you can't limit a service's boundary to within a module. And singleton classes are not always what you want.

But since Angular 6, there are two other recommended ways of providing services. One is using providedIn:

```typescript
@Injectable({
  providedIn: 'root',
})
export class SomeService {
}
```

providedIn has two benefits. First, it enables **tree shaking**, which is if this service is used nowhere, it won't be bundled into the app at all. Second, it allows **lazy loading** if the service is only used in a lazy-loading module.

I mentioned Singleton services are not always wanted. If a service is specified in the providers of the consuming component itself, it's created everytime:

```typescript
@Component({
  /* . . . */
  providers: [SomeService]
})
```

### Namespace

NgModule is like a **Java Package**. It breaks down an Angular app into a more granular level. A well defined Angular app contains serveral feature modules. Each module has its in and out. In is imports of external libraries, Out is exports of components, directives, pipes or services. By wiring up different modules together, an app is built up.