---
layout: post
title:  "Angular Reactive Form with Fluent Validations"
categories: "angular reactive fluent"
---

In Angular, [Form Validation](https://angular.io/guide/form-validation) is super simple. Especially with Reactive form, validation is a built-in capability.

However, it doesn't justify that front-end validation could replace the server-side validation. On the contrary, the API level model validation is a must-to-do, because it's the real guard of the database and server. In ASP.NET Core, [Fluent Validation](https://fluentvalidation.net/) is the de facto validation framework of the server.

Hence, the question becomes how could we incorporate Fluent validation into the Reactive form, and display the server-side validation messages properly in the fields of the form?

To start with, let's take a look at the Fluent validation HTTP response. By default, it looks like below:

<table border="1" cellspacing="0" cellpadding="5" style="margin-bottom: 10px;">
    <tr bgcolor="#F5DEB3">
        <th>Status Code</th>
        <th>Body</th>
    </tr>
    <tr>
        <td>400 (Bad Request)</td>
        <td>
            {
              "Username": [
                "Username should not be empty."
              ]
            }
        </td>
    </tr>
</table>

The response body is a JSON of key-value pair. The key is the property name of the Model, which failed validation. The value is an array of error message strings, which could be specified in 'WithMessage'.

### 1. How to set Fluent validation errors in Reactive form ###

When the form is submitted, wait for the HTTP response, if the HTTP response is a 400 Bad Request, set Fluent validation errors.

```typescript
submitFormRequest()
    .catch(err => {
        if (err.status === HttpStatus.BadRequest) {
            // Bad Request response
            this.setFluentValidationErrors(this.submitForm, err.error);
        }
    });
```

```typescript
/**
 * Set Fluent Validation Errors
 * @param {FormGroup} form - form validation group
 * @param {any} err - fluent validation error
 */
setFluentValidationErrors(form: FormGroup, err: any): void {
    Object.entries(err).forEach(([key, value]) => {
        const control = form.get(key);
        if (!!control) {
            control.setErrors({
                fluentValidationError: (value as Array<string>).join('\r\n')
            });
        }
    });
}
```

As you may tell, the above method 'setFluentValidationErrors' has several <span style="color: red">limitations</span>.

**Firstly, the form controls must be flat, not deep nested.** If the form group has nested form groups, the get by key method could not locate the nested form control. Let's take a look at following form:

```typescript
this.submitForm = this.formBuilder.group({
    Username: [],
    Password: []
});
```
We are able to locate say Username form control by:

```typescript
this.submitForm.get('Username');
```

And **'Username'** is exactly the key of the Fluent validation error response. Therefore, there is no problem to set Fluent errors back in Username form control.

However, what if the submit form looks like below, in which **Username** is in the nested FormGroup **Profile**:

```typescript
this.submitForm = this.formBuilder.group({
    Profile: this.formBuilder.group({
        Username: []
    }),
    Password: []
});
```

The only way to locate Username form control is by:

```typescript
this.submitForm.get('Profile.Username');
```

Thus, get form control by Fluent validation key is not working, because Fluent validation key is **Username**.

Actually, a good way is to break down a deep nested FormGroup into flat reusable FormGroup. Here is a blog I highly recommend [Building Reusable Forms in Angular](https://coryrylan.com/blog/building-reusable-forms-in-angular).

**Secondly, we should not use the PascalCase property name to name the form control.**

In the above example, the form control is named **Username**, which is the same as the property name in the C# class. It is PascalCase, in which the first letter of each word is capitalized. However, in Javascript, the naming convention is [CamelCase](https://en.wikipedia.org/wiki/Camel_case), in which the first letter of the first word is in lower case. 

In addition, in Reactive form, the FormGroup usually has the same structure as form model, so that it's easy to set value in the form via **patchValue** or **setValue**.

```typescript
this.submitForm.patchValue(model); // or
this.submitForm.setValue(model);
```

Please read [Updating Angular 2 Forms with patchValue or setValue](https://ultimatecourses.com/blog/angular-2-form-controls-patch-value-set-value) to learn more.

To have the same structure as form model means the names of the FormGroup and FormControl are CamelCase, starting with lowercase letter. Because the JSON model sent from the server is CamelCase. Hence, the submit form should look like:

```typescript
this.submitForm = this.formBuilder.group({
    username: [],
    password: []
});
```

However, the Fluent validation error key is starting with Uppercase, the same as the property name in C# class.

To convert the Fluent PascalCase key to Javascript CamelCase key, we have to modify the **'setFluentValidationErrors'** method.

```typescript
Object.entries(err).forEach(([key, value]) => {
    const control = form.get(key.replace(/^[A-Z]./, ($1) => $1.toLowerCase()));
    if (!!control) {
        control.setErrors({
            fluentValidationError: (value as Array<string>).join('\r\n')
        });
    }
});
```

### 2. How to clear Fluent validation errors on user input ###

When user enters in the input field, don't forget to clear the Fluent validation errors. The easiest way is to subscribe to value changes in the FormControl.

```typescript
/**
 * Clear fluent validation errors at valuesChange
 * @param {FormControl} control - Form Control
 */
protected subscribeValuesChange(control: FormControl) {
    this.subscriptions.push(control.valueChanges.subscribe(value => {
        if (control.hasError('fluentValidationError')) {
            control.updateValueAndValidity();
        }
    }));
}
```

When the value is changed in the input FormControl, update the validity of the FormControl. We can loop through the FormControls of the FormGroup to do it. Again, it relies on that there are no nested FormGroups.

```typescript
Object.keys(this.submitForm.controls).forEach(key => {
  this.subscribeValuesChange(this.submitForm.get(key));
});
```

Finally, on component destroy, don't forget to unsubscribe the subscriptions.

```typescript
ngOnDestroy(): void {
    for (let listener of this.subscriptions) {
        listener.unsubscribe();
    }
}
```