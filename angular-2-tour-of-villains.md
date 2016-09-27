Angular 2 Tour of Villains
==========================

Follow the steps from [Angular 2 in 5.5 Minutes](angular-2-in-5-minutes.md), renaming the folder `angular-2-tour-of-villains`
```
angular-2-tour-of-villains
├─ app
│   ├─ app.components.ts
│   └─ main.ts
├─ node_modules ...
├─ typings ...
├─ index.html
├─ package.json
├─ tsconfig.json
└─ typings.json
```

Transpile and run!  
`npm start`

This command runs the compiler in watch mode, starts the server, launches the app in browser, makes evil plans, and keeps the app running.

## Show Our Villains
We want to display Villain data in our app.
Let's add two properties to our `AppComponent`, a `title` property for the application name and a `villain` property for a villain named 'Shytestorm'.

### app.components.ts
```
export class AppComponent {
  public title = 'Tour of Villains';
  public villain = 'Shytestorm';
}
```

Now update the template in the `@Component` decoration with data bindings to these new properties.
```
template: '<h1>{{title}}</h1><h2>{{villain}} details!</h2>'
```
The browser should refresh and display our title and hero.
The double curly braces tell our app to read `title` and `villain` properties from the component and render them. Interpolation! One-way data binding FTW.

### Villain interface
At the moment, our villain is just a name and an evil intent. Our villain needs more properties. Let's make Villain an interface.

### app.components.ts
```
interface Villain {
  id: string;
  name: string;
}
...
```

Until Villain has evil behavior, an interface will work just fine. Time to instantiate our Villain.

### app.components.ts
```
...
  public villain: Villain = {
    id: 'id1',
    name: 'Shytestorm'
};
```
Update the html template bindings.
```
template: '<h1>{{title}}</h1><h2>{{villain.name}} details!</h2>'
```
The browser refreshes and continues to display our villain's name.
### Adding more html
Names are good, but we need properties. Let's add some `<div>`action. ES2015 + TypeScript allows for multiline template strings. Just change the quotes to back ticks and hit return to your little hearts content.
```
template:`
  <h1>{{title}}</h1>
  <h2>{{villain.name}} details!</h2>
  <div><label>id: </label>{{villain.id}}</div>
  <div><label>name: </label>{{villain.name}}</div>
`
```

### Editing our Villain
Shytestorm is considering a name change; Shytestorm might be offensive, but it just isn't evil. Let's add a feature to allow our villain's name to be edited in a textbox.

Refactor the villain name `<label>` and add an `<input>` element bound to the `ngModel`.

```
<div>
  <label>name: </label>
  <div><input [(ngModel)]='villain.name'></div>
</div>
```

We can see out changes reflected as soon as we make them. Please, for the love of all that is unholy, change Shytestorm's name!!!

### What's this `[(ngModel)]` thing?

Pay careful attention to the syntax we used to invoke the 2-way binding magic and all shall be revealed. The brackets `[]` indicate that the enclosed value is a one-way binding from the model to the view. The parentheses `()` indicate that the enclosed value is a one way binding from the view to the model. Combining the two `[()]` then signifies a two-way binding and a **flow of data in both directions**.

Great. We get the bunch of bananas in a box thing `[()]`, but what is `ngModel`? Well, it's just a directive that wraps an elements `value` property, listens for an `input` event, and raises its own event with the changed text. In short, `ngModel` avoids us having to write something like:
```
<input [value]='villain.name' (input)='villain.name=$event.target.value'>
```
Cause who wants to write that?

## The Villainy We've Perpetrated
To summarize, we've:
1. Used double curly brace `{{}}` one-way data binding to display `Villain` properties
2. Wrote a bad-ass multiline template with template strings (thanks ES2015!)
3. Learned how to change a villain's name using two-way data binding and the `ngModel` directive

Here's what the the `app.component.ts` ought to look like now:
### app.component.ts
```
import {Component} from 'angular2/core';

interface Villain {
  id: number;
  name: string;
}

@Component({
selector: 'my-app',
template:`
  <h1>{{title}}</h1>
  <h2>{{villain.name}} details!</h2>
  <div><label>id: </label>{{hero.id}}</div>
  <div>
    <label>name: </label>
    <input [(ngModel)]='villain.name'>
  </div>
`

})

export class AppComponent {
  public title = 'Tour of Villains';
  public villain : Villain = {
    id: 'id1',
    name: 'Shytestorm'
  };
}

```

## Next Steps: Moar Villainy
We really need more than one villain to really make an Evil Difference™. Let's make a whole list of villains [next](angular-2-moar-villains.md).
