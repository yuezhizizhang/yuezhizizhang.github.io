---
layout: post
title:  "Is Angular MVC or MVVM"
categories: "Angular6 MVC MVVM"
---

Is Angular MVC or MVVM? Well, it's worth looking at what's MVC and what's MVVM.

### What's MVC?

Model, View and Controller. What's the role of a Controller? It's the mediator between View and Model. Then what does mediator do?

Instead of answering this question directly, let's date back to 2011 and think about what you used to write in a Controller. Back to 2011, there used to be a library every front-end develop was familiar with, which is **jQuery**.

**jQuery** libary makes the manipulation of DOM tree, handling events and sending Ajax calls so easy. It dominated the front-end development.

*BINGO!* **jQuery** is the library you heavily used at writing a Controller. So what does a mediator do? **It manipulates the DOM tree, handles events and makes Ajax calls**.

For instance, when the model is updated, you have to manipulate the DOM tree to refresh the View. And when the user interacts with the View, e.g., clicking a button or filling a form, you have to handle the events, either click event or input change event. When user submits a form, you have to Ajax post the form data to the backend.

### What's MVVM?

Haha, MVVM is an idea proposed by Microsoft. It firstly comes up in WPF applications. I have worked on WPF app for over a year. Here is some of my understanding of MVVM. 

MVVM stands for Model, View and ViewModel. See, ViewModel is the core difference from MVC. What's the role of a ViewModel?

In WPF app, XAML is equivalent to HTML as the markup language. Every XAML file comes with a **xaml.cs** file. This xaml.cs file is the ViewModel.

xaml.cs is a very thin container. It contains all of the models binding to the View. Yes, data binding is essential in XAML. I have an old blog discussing about [XAML Data Binding](http://yuezhizizhang.github.io/wpf/xaml/c%23/2017/02/20/wpf-data-binding.html). Apart from a container role, ViewModel should do nothing more than some rendering or layout work. xmal.cs doesn't deal with events, because Microsoft recommends using **Commands** to handle events.

### Is Angular MVC or MVVM?

In Angular, the basic building block is **Component**. Every Component is composed of three parts, a component.html, a component.ts and a style file. From this perspective, Component is really like XMAL. And same as XAML, data binding is essential in Angular. Hence, there is rare time you have to manipulate DOM by yourself. And in Angular, we usually inject service class to deal with Ajax or http calls.

So the remaining question is how about events handling? In my codes, I generally handle events in the component.ts itself. But, you can inject service to handle events too. Since **Angular 6**, there is new way of injecting service.

```typescript
@Component({
  selector:    'app-hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ HeroService ]
})
```

In this way, the service is not a singleton service anymore. Instead, it's a new instance every time a new component is created. I believe thus you can define services as commands.

In conclusion, my opinion is Angular is more MVVM than MVC.