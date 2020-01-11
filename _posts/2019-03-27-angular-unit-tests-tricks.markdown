---
layout: post
title:  "Angular Unit Testing Tricks"
categories: "Angular6 Unit-Testing"
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
const context = require.context('./', true, /\/app\/user\/user\.component\.spec\.ts$/);
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

### No4. How to get element by id?

There are two ways of query element by id. The first one is easier to Google out:

```typescript
/**
 * @param id - '#id'
 * @return DebugElement
 */
function getElementById(id: string): DebugElement {
  return fixture.debugElement.query(By.css(id));
}
```

The above method returns a DebugElement. To access the DOM element of it, use property *'.nativeElement'*.

---

<span style="color: red; font-weight: bold;">&gt;&gt;&gt; Revised <br> The following one is what I recently learnt from Angular documents and I highly recommend:</span>

```typescript
/**
 * @param id - '#id'
 * @return native DOM element
 */
function getElementById(id: string): any {
  return fixture.nativeElement.querySelector(id)
}
```

It returns the native DOM element directly.

### No5. How to click a button?

First, locate the button by class name or id. Then, click it.

```typescript
const btn = fixture.nativeElement.querySelector('#id');
btn.click();
```

### No6. How to get the service instance?

Suppose a UsersService is injected into your component. How to get the service instance?

```typescript
const service: UsersService = fixture.debugElement.injector.get(UsersService);
```

---

<span style="color: red; font-weight: bold;">&gt;&gt;&gt; Revised <br> The following one is what I recently learnt from my new project and I highly recommend:</span>

```typescript
const service: UsersService = TestBed.get(UsersService);
```

### No7. How to test data binding?

Data binding is two way. [] is from the component property to the View. For instance, you have a label which is binding to name property. When the name property is updated, we want to make sure the label is updated with the new value.

```typescript
const dummyName: string = 'newName';
component.name = dummyName;
// Update the UI
fixture.detectChanges();
const label = fixture.nativeElement.querySelector('#label-id');
expect(labe.textContent).toEqual(dummyName);
```

**fixture.detectChanges()** is doing the trick. Anytime you update the component properties, and hoping the UI is reflecting the changes properly, do call detectChanges to update the UI.

The other way around is () via event notification. It's a bit tricky about the input value binding. Suppose you have a search box, which is a text input. You are binding the **(ngModelChange)** of the input box to some filter methods. How to validate at user input, the filter method is called? A cheating way is:

```typescript
const filterSpy: any = spyOn(component, 'filter');
const searchInput: DebugElement = fixture.debugElement.query(By.css('#search-input'));
searchInput.nativeElement.dispatchEvent(new Event('ngModelChange'));
expect(filterSpy).toHaveBeenCalled();
```

This looks a bit nasty. We are dispatching the ngModelChange event directly. However, at least this is working after I have tried a hundred other ways which just don't work. You know, sometimes, wrtiting unit testing is very ***frustrating***.

### No8. How to test asynchronous method?

Suppose we have a users service method, which pulls the number of users from the backend. It returns a Promise which contains the total number of users. How do you stub this asynchronous method?

```typescript
it('Stub the asynchronous method and validates the response', fakeAsync(() => {
  const dummyCount: number = 18;
  // Stub the service method
  const service: UsersService = TestBed.get(UsersService);
  spyOn(service, 'getTotalCount').and.returnValue(Promise.resolve(dummyCount));
  // ngOnInit
  fixture.detectChanges();
  // Mimic asynchronous response
  flush();
  expect(component.totalCount).toEqual(dummyCount);
}));
```

**fakeAsync** and **flush** are doing the tricks here.

### No9. How to mock ngx-translate TranslateService?

[ngx-translate](https://github.com/ngx-translate/core) is an internationalization (i18n) library. I don't use Angular i18n, because the production build of it only supports AOT compilation. AOT compilation means the locale resources are compiled into JavaScript. Hence, you can't change the locale JSON file on the fly. 

By using ngx-translate, to translate strings in the typescript file, you have to inject TranslateService into the component. Then you can use the **instant** method in the typescript to get the translated string. To mock TranslateService in the component.spec.ts.

In the component.spec.ts:
```typescript
{ provide: TranslateService, useClass: TranslateServiceMock }
```

translate.mock.service.ts:
```typescript
export class TranslateServiceMock {
  instant(key: string, interpolateParams?: Object): string {
    return key;
  }
}
```

---

<span style="color: red; font-weight: bold;">&gt;&gt;&gt; Revised <br> The following one is what I recently learnt from my new project and I highly recommend:</span>

In the component.spec.ts:
```typescript
const fakeTraslateService = jasmine.createSpyObj<TranslateService>('TranslateService', ['instant']);

{ provide: TranslateService, useValue: fakeTraslateService }
```

jasmine.createSpyObj creates a mock 'TranslateService' and adds a spy on its 'instant' method.

Suppose later in your test cases, you suppose instant to return different values, you can do it easily by:

```typescript
const stubTranslate = (value) => fakeTraslateService.instant.and.returnValue(value);
```

### No10. How to mock ngx-translate pipe?

If you are using ngx-translate pipes, for instance:

```html
<button id="close-btn"
        i18n-title="@@close"
        title="Close"
        [title]="'close' | translate">
</button>
```

Then in the component.spec.ts, you have to mock the translate pipe:

```typescript
@Pipe({ name: 'translate' })
class TranslatePipeMock implements PipeTransform {
    transform(value: string): string {
        return value;
    }
}
```

And declares it while setting up the testing module:

```typescript
declarations: [
  ...
  TranslatePipeMock
],  
```

### No11. How to make the unit test codes DRY?

The simplest way is writing the repeated codes into functions. For instance, getElementById could be an arrow function.

```typescript
const getElementById = (id) => fixture.nativeElement.querySelector(id);

const getService = (service) => TestBed.get(service);
```

### No12. How to test Observable?

Test Observables as if they are not asynchronous. An simple example is to fetch the query parameters from the route.

```typescript
const route: ActivatedRoute = TestBed.get(ActivatedRoute);
route.queryParams = of({ uuid: '12344555' });
```