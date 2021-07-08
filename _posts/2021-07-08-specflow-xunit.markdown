---
layout: post
title:  "SpecFlow with xUnit.net"
categories: "Gherkin BDD SpecFlow xUnit.net"
---

In 2006, Dan North published an article called ["Introducing BDD"](https://dannorth.net/introducing-bdd/){:target="_blank"}, introducing behavior-driven development. In the article, Dan suggested to write tests in real sentences, so that they could be appreciated not only by developers and testers, but also by business users and analysts. In addition, Dan defined a "ubiquitous language" for writing the story behaviors. Dan wrote:

> A story’s behaviour is simply its acceptance criteria – if the system fulfills all the acceptance criteria, it’s behaving correctly; if it doesn’t, it isn’t.

> We started describing the acceptance criteria in terms of scenarios, which took the following form:

```
Given some initial context (the givens),
When an event occurs,
Then ensure some outcomes.
```

It is the initial prototype of **Gherkin** syntax. Now, **Gherkin** has become the ubiquitous language for writing behavior scenarios. Here is a complete [Gherkin Reference](https://cucumber.io/docs/gherkin/reference/){:target="_blank"}. Gherkin can be written not only in English, but in a number of other languages. This [localization](https://cucumber.io/docs/gherkin/languages/){:target="_blank"} capability improves the readability of Gherkin.

We wrote business readable documentation in Gherkin. It helps us document what features the system has, how does the feature work and what different usage scenarios the system supports. This documentation lives with the source code. Under the scene, these documentation are executable automated tests.

**SpecFlow** is a BDD framework for .NET. It uses the official Gherkin parser, and tied the test scenarios and steps to the application c# codes. 

To work with SpecFlow in Visual Studio, the first step is to install the SpecFlow Visual Studio extension. Here is an [instruction](https://docs.specflow.org/projects/getting-started/en/latest/GettingStarted/Step1.html){:target="_blank"} on how to install it. After the extension is installed, right click in the feature file, the pop-up menu contains "Generate Step Definitions".

To run SpecFlow with xUnit.net testing framework, we have to install the following NuGet packages:

* SpecFlow
* SpecFlow.xUnit
* SpecFlow.Tools.MsBuild.Generation
* xunit
* xunit.runner.visualstudio

![specflowNuget](/assets/2021-07-08-specflow-nuget.png "specflowNuget")

Pluralsight has a very good course on SpecFlow [Business Readable Automated Tests with SpecFlow 2.0](https://www.pluralsight.com/courses/specflow-2-0-business-readable-automated-tests){:target="_blank"}.

Or else [@astridraina](https://medium.com/@astridraina){:target="_blank"} has [4 sessions of tutorials](https://medium.com/@astridraina/specflow-part-1-the-first-steps-9f762302af8c){:target="_blank"} on SpecFlow which are equivalently good.