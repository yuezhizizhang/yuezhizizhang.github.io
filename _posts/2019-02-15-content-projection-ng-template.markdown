---
layout: post
title:  "Angular Content Projection and ng-template"
categories: "Angular6"
---

To start with, I highly recommend a UX library to you, which is [Clarity Design System](https://github.com/vmware/clarity). It contains almost all of the UX pieces you would need to hook up a website. Indeed an awesome project!

### What's the benefit of Content Projection?

From my experience, the biggest benefit of Content Projection is for localization purpose. Suppose a component has a button, the text of which could either be 'Save' or 'Submit'. 

How do you make the button text customizable? One approach might be making the button text an **@Input** string parameter and binding it to the UI.

```html
{% raw %}
<button id="save-btn"
        type="button"
        class="btn btn-primary">
    <span>{{buttonText}}</span>
</button>
{% endraw %}
```

The problem with it is, you have to localize the button text in typescript. There is a known issue with Angular i18n about translating strings outside a template. [Issue 11405 i18n: Able to use translation strings outside a template](https://github.com/angular/angular/issues/11405). It's not that you can't do it. But the workaround is not straightforward.

However, with content projection, the solution is easy and straightforward.

***confirmation.component.html***
```html
{% raw %}
<button id="save-btn"
        type="button"
        class="btn btn-primary">
    <ng-content></ng-content>
</button>
{% endraw %}
```

The button content is projected out. To specify the localized button text, we simply do:

```html
<confirmation>
    <span i18n>Submit</span>
</confirmation>
```

In addition to just displaying text, we can also nest spinner icons inside the button.

```html
<confirmation>
	<span i18n>Submit</span>
    <span class="spinner spinner-inline"></span>
</confirmation>
```

The display of the button becomes fully customizable.

### How to project multiple content out?

Multiple Content Projection is easy. One of the approaches is through class selector. Suppose I have a users datagrid component '**&lt;users-datagrid&gt;**'. This component lets the consumer to determine the last column, including both the header and the cell.

***users-datagrid.component.html***
```html
{% raw %}
<clr-datagrid>
    <clr-dg-column>User ID</clr-dg-column>
    <clr-dg-column>Name</clr-dg-column>
    <clr-dg-column>Creation date</clr-dg-column>
    <clr-dg-column>
    	<ng-content select=".last-column-header"></ng-content>
    </clr-dg-column>

    <clr-dg-row *ngFor="let user of users">
        <clr-dg-cell>{{user.id}}</clr-dg-cell>
        <clr-dg-cell>{{user.name}}</clr-dg-cell>
        <clr-dg-cell>{{user.creation | date}}</clr-dg-cell>
        <clr-dg-cell>
            <ng-content select=".last-column-cell"></ng-content>
        </clr-dg-cell>
    </clr-dg-row>
</clr-datagrid>
{% endraw %}
```

There are two class selectors, one is '.last-column-header', the other is '.last-column-cell'. Hence, in the consumer component, we could specify the header and the cell in such a way:

***consumer-users-datagrid.component.html***
```html
<users-datagrid>
    <span class="last-column-header">
        Remove
    </span>
    <span class="last-column-cell">
        <button id="remove-btn"
                class="btn btn-icon btn-link">
            <clr-icon shape="trash" class="btn-icon-pos"></clr-icon>
        </button>
    </span>
</users-datagrid>
```

Thus, in the last column, the header is 'Remove', while on each row, there is a trash bin icon.

### What's the limitation of Content Projection?

Content Projection is meant for static content. There is no way (At least I haven't found yet) to pass the scoped data out.

Let's continue with the datagrid example. Suppose in the cosumer component, it wants to display the user's gender in the last row, just like below:

***consumer-users-datagrid.component.html***
```html
{% raw %}
<users-datagrid>
    <span class="last-column-header">
        Gender
    </span>
    <span class="last-column-cell">
        {{user.gender}}
    </span>
</users-datagrid>
{% endraw %} 
```

Well, user is ***undefined***! You can't pass the **user** variable out from the datagrid component itself to its consumer.

### ng-template

In such a use case, you must use ng-template instead of ng-content. This blog [Creating Reusable Components with NgTemplateOutlet in Angular](https://alligator.io/angular/reusable-components-ngtemplateoutlet/) has explained how to do it. In our example, it has to be:

***users-datagrid.component.html***
```html
{% raw %}
<clr-datagrid>
    ...
    <clr-dg-row *ngFor="let user of users">
        ...
        <clr-dg-cell>
            <ng-container *ngTemplateOutlet="datagridCellTemplate; context: {$implicit: user}"></ng-container>
        </clr-dg-cell>
    </clr-dg-row>
</clr-datagrid>
{% endraw %}
```

***users-datagrid.component.ts***
```typescript
@Component({
    selector: 'users-datagrid',
    templateUrl: './users-datagrid.component.html',
})
export class UsersDatagridComponent {
    @ContentChild(DatagridCellDirective, { read: TemplateRef }) datagridCellTemplate;
    ...
}
```

The ***key*** here is to create a directive:

***datagrid-cell.directive.ts***
```typescript
@Directive({
    selector: '[datagridCell]'
})
export class DatagridCellDirective { }
```

**consumer-users-datagrid.component.html***
```html
{% raw %}
<users-datagrid>
    <ng-template datagridCell let-user>
        {{user.gender}}                        
    </ng-template>
</users-datagrid>
{% endraw %} 
```