# Angular code guidelines
=
---
#### By following these guidelines, you can write cleaner, more maintainable, and more scalable code in Angular 12.

---

## Project structure
```
|-- app
|   |-- shared
|   |   |-- animations
|   |   |-- components
|   |   |-- custom-decorators
|   |   |-- directives
|   |   |-- enums
|   |   |-- guards
|   |   |-- helpers
|   |   |-- models
|   |   |-- pipes
|   |   |-- services
|   |-- views

```

- `shared` SharedModule is the module with the animations, components, directives, enums, pipes, guards, helpers, models, services that you use
  throughout your app. This module should consist completely of declarations, most of
  them exported
- `views` ViewsModule is the module with the components that are used only in one
  place.


## Using Sass
Use Sass over css for styling your components.

## Use smart and dumb components
In Angular, smart components and dumb components are two distinct types of components.
Smart components are responsible for handling data and business logic, 
while dumb components focus on rendering the UI based on inputs received from their parent component.

#### Here's an example to illustrate the difference between smart and dumb components:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-smart-component',
  template: `
    <h1>Smart Component</h1>
    <app-dumb-component [message]="message"></app-dumb-component>
    <button (click)="updateMessage()">Update Message</button>
  `
})
export class SmartComponent {
  message = 'Initial Message';

  updateMessage() {
    this.message = 'New Message';
  }
}
```
In the example above, SmartComponent is a smart component because it has a state variable message 
and a method `updateMessage()` that updates the state. 
It also renders a dumb component DumbComponent passing it the message as an input property.
    
```typescript
import { Component, Input } from '@angular/core';

@Component({
selector: 'app-dumb-component',
template: `
<h2>Dumb Component</h2>
<p>{{message}}</p>
`
})
export class DumbComponent {
@Input() message: string;
}
```
On the other hand, DumbComponent is a dumb component because 
it only receives inputs from its parent and renders the UI based on those inputs.

By separating smart and dumb components, we can create more maintainable and testable code by reducing the complexity of each component 
and making it easier to reason about its responsibilities.

## Avoid nested subscriptions
Avoid using nested subscriptions as they can lead to memory leaks and make your code difficult to read and maintain. 
Instead, use the `switchMap` operator to flatten your observables and avoid nested subscriptions. For example:
    
```typescript

this.userService.getUser()
.pipe(
switchMap(user => this.orderService.getOrders(user.id))
)
.subscribe(orders => console.log(orders));

```

## User `ng-container` for directives
Use `ng-container` to group multiple directives on a single element without creating an extra element in the DOM.
This can help you write cleaner HTML and reduce the number of unnecessary elements in your code. For example:
        
```typescript
<ng-container *ngIf="showUser; else login">
  Welcome, {{ user.name }}
</div>

<ng-template #login>
<button (click)="showLoginForm()">Login</button>
</ng-template>
```
## Use Reactive Forms over Template Driven Forms:
Use reactive forms over template-driven forms as they provide better type safety, scalability, and testability. 
Reactive forms allow you to define your form model in TypeScript and handle form validations programmatically.
For example:
```typescript
import { FormBuilder, Validators } from '@angular/forms';

export class OrderFormComponent {
  form = this.fb.group({
    name: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]],
    phone: ['', Validators.pattern(/^\d{10}$/)],
  });

  constructor(private fb: FormBuilder) {}

  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```
## User lazy loading for feature modules
Use lazy loading to improve the performance of your app by splitting it into multiple bundles 
and only loading the code that is required for the initial render.

## Use `OnPush` change detection strategy
Use `OnPush` change detection strategy for components that don't need to update the view on every change detection cycle.

## Use `async` pipe for observables
Use the `async` pipe to subscribe to observables in your templates and avoid the need to manually handle subscriptions.
For example:
```typescript
<ul>
  <li *ngFor="let user of users$ | async">
    {{ user.name }}
  </li>
</ul>
```

## Use `trackBy` for `*ngFor`
Use `trackBy` to improve the performance of `*ngFor` by identifying items in the iterable
differing only by their reference. For example:
```typescript
<ul>
  <li *ngFor="let item of items; trackBy: trackByFn">
    {{ item.name }}
  </li>
</ul>
```

