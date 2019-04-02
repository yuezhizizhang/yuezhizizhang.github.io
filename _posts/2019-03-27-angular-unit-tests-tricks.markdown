---
layout: post
title:  "Angular Unit Testing Tricks"
categories: "Angular 6, Unit Testing"
---

A summarization of the unit testing works has been on my mind for some time. After writing almost 400 unit test cases, covering 2700+ lines of codes, I feel I have more confidence to say about it. I wouldn't go to the basics, explainning what's Karma, what's Jasmine. Instead, I will simply list the tricks I use everyday to deal with different testing cases.

### No1. How to Debug tests?

Normally, we run unit tests using **Headless Chrome**. However, when the testing codes go wrong and crash the browser, you desperately want to figure out why. At this moment, probably the easiest way is to open the console output of Chrome and see what's going wrong. To do that, you must run tests with **Chrome**, not Headless Chrome. In addition, If you wish to set a breakpoint at the codes, you must make sure set **watch** to true or **singleRun** to false. Thus, when the codes are loaded, go to Sources tab of Chrome and set your breakpoints. Later on, refresh the page to re-run the tests and hit your breakpoints.

```cmd
ng test --browser=Chrome --singleRun=false
ng test --browser=Chrome --watch=true
```

### No2. How to test a single spec file?

You are adding new test cases to your user.component.spec.ts file. You want to run them to see if they are working. Damn it. You already have hundreds of testing cases ahead. Hence, you are sitting down and counting your fingers, hoping your newly added cases could be hitted sooner. How to run a single spec file, namely, user.component.spec.ts? Go to test.ts file, commenting the following line:

```typescript
// Then we find all the tests.
const context = require.context('./', true, /\/app\/.*\.ts$/);
```

Instead, specify your spec file in the context:
```typescript
const context = require.context('./', true, /\/app\/user\/user\.compoent\.spec\.ts$/);
```

### No3. How to test ngOnInit?

**ngOnInit** initializes an Angular Component. It's probably the most important step in the lifecycle of a component. Within **ngOnInit**, we usually **Ajax** pull data from backend and setup the UI. I want to remind you in the unit testing, there are two ways to trigger ngOnInit:

```typescript
// Call it directly
component.ngOnInit();
// The other way is used more frequently
fixture.detectChanges();
```

If you want to test a component's status before initialization, DO NOT call **fixture.detectChanges()**.

### No4. How to click a button?

First, locate the button by class name or id. Then, click it.

```typescript
const btn: DebugElement = fixture.debugElement.query(By.css('#id'));
btn.nativeElement.click();
```

### No5. How to get the service instance?

Suppose a UsersService is injected into your component. How to get the service instance?

```typescript
const service: UsersService = fixture.debugElement.injector.get(UsersService);
```

### No6. How to test data binding?

Data binding is two way. [] is from the component property to the View. For instance, you have a label which is binding to name property. When the name property is updated, we want to make sure the label is updated with the new value.

```typescript
const dummyName: string = 'newName';
component.name = dummyName;
// Update the UI
fixture.detectChanges();
const label: DebugElement = fixture.debugElement.query(By.css('#label-id'));
expect(labe.nativeElement.textContent).toEqual(dummyName);
```

**fixture.detectChanges()** is doing the trick. Anytime you update the component properties, and hoping the UI is reflecting the changes properly, do call detectChanges to update the UI.

The other way around is () via event notification. It's a bit tricky about the input value binding. Suppose you have a search box, which is a text input. You are binding the **(ngModelChange)** of the input box to some filter methods. How to validate at user input, the filter method is called? A cheating way is:

```typescript
const filterSpy: any = spyOn(component, 'filter');
const searchInput: DebugElement = fixture.debugElement.query(By.css('#search-input'));
searchInput.nativeElement.dispatchEvent(new Event('ngModelChange'));
fixture.detectChanges();
expect(filterSpy).toHaveBeenCalled();
```

This looks a bit nasty. We are dispatching the ngModelChange event directly. However, at least this is working after I have tried a hundred other ways which just don't work. You know, sometimes, wrtiting unit testing is very ***frustrating***.

### No7. How to test asynchronous method?

Suppose we have a users service method, which pulls the number of users from the backend. It returns a Promise which contains the total number of users. How do you stub this asynchronous method?

```typescript
it('Stub the asynchronous method and validates the response', fakeAsync(() => {
  const dummyCount: number = 18;
  // Stub the service method
  const service: UsersService = fixture.debugElement.injector.get(UsersService);
  spyOn(service, 'getTotalCount').and.returnValue(Promise.resolve(dummyCount));
  // ngOnInit
  fixture.detectChanges();
  // Mimic asynchronous response
  flush();
  expect(component.totalCount).toEqual(dummyCount);
}));
```

**fakeAsync** and **flush** are doing the tricks here.

### No8. How to make the unit test codes DRY?

The simplest way is making the repeated codes into functions. For instance, getDebugElement could be a function. StubGetUsers could be another function.

```typescript
function getDebugElement(fixture: any, id: string): DebugElement {
  return fixture.debugElement.query(By.css(id));
}

function getService(fixture: any, service: any): any {
  return fixture.debugElement.injector.get(service);
}

function stubGetUsersCount(count: number): jasmine.Spy {
  const service: UsersService = getService(fixture, UsersService);
  return spyOn(service, 'getTotalCount').and.returnValue(Promise.resolve(count));
}
```

Place these functions into whatever describe block which fits.