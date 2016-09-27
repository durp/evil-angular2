Angular 2 Components of Evil
============================
Our power is growing. Requests are flowing in for reuseable, dynamic components and more reuseable assets. Let's separate the villains list from the villain details and make the details component reuseable.

## Brief Recap
Here is what your app structure should look like:
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
### Keep the app transpiling and running
If you ain't already, do it now
`npm start`
## Making a Villain Detail Component
Our list and detail are both defined in the same file. Right now we are violating the Single Responsibility Principle:
> Gather together the things that change for the same reasons.
> Separate those things that change for different reasons.

Breaking the components apart will allow each component to evolve separately. And evolving separately is half the battle.

### Separating the Hero Detail Component
Add a new file called `villain-detail.component.ts` to the `app` folder and create the `VillainDetailComponent`.

### villain-detail.component.ts (first pass)
```
import {Component} from 'angular2/core';
@Component({
  selector: 'my-villain-detail'  
})
export class VillainDetailComponent {
}
```
> __Naming Conventions__
> Being able to easily identify component classes and which files contain components is a *Good Thing™*
> All component names end in "Component"; all component file names end in ".component". Component, component, .component.
> File names are lower dash (kebab) case because some servers are stupid.

We first import the `Component` function. We'll need this to decorate our component metadata. The `@Component` decorator is where we specify the selector that identifies the component's element. But what use is a selector without a template? None!

### Villain Detail Template
Let's extract the Villain Detail section from the `AppComponent` template and put it into our new component.
### villain-detail.component.ts (template)
```
template:`
  <div *ngIf="selectedVillain">
    <h2>{{selectedVillain.name}} details!</h2>
    <div><label>id: </label>{{selectedVillain.id}}</div>
    <div>
      <label>name: </label>
      <input [(ngModel)]="selectedVillain.name" placeholder="name"/>
    </div>
  </div>
`
```
__Add the *villain* property__
`public villain: Villain;`

Poop. Our `Villain` type declaration is over in the `app.components.ts`; what to do? Simple. Just extract it into its own `villain.ts` file.
### villain.ts (Exported Villain interface)
```
export interface Villain {
  id: number;
  name: string;
}
```
Now we can import the `Villain` wherever we need it. Go ahead and add the import to the `app.component.ts` and `villain-detail.component.ts`.
### villain-detail.component.ts and app.component.ts (Import the Villain interface)
`import {Villain} from './villain'`
Notice that we don't need the '.ts' extension on the import statement.
Now we need to wire the `VillainDetailComponent` and its parent `AppComponent` together. Let's briefly discuss; how might we might do this?
We *might* bind like this:
`<my-villain-detail [villain]="selectedVillain"></my-villain-detail>`
The square brackets `[ ]` enclosing the `villain` property indicate that it is a *target* binding. *Target* bindings *must* be an *input* property or Angular will get pissy - [for a damn good reason](https://angular.io/docs/ts/latest/guide/attribute-directives.html#!#why-input) - basically components or directives used as *target* properties are protected to ensure that only their public API properties can be bound.
OK, so now we have to declare `villain` as an input. There are a few ways to do that, we are just going to do this by creating an `inputs` array in the `@Component` metadata and shoving it in.
`inputs: ['villain']`
## Refresh the AppComponent
Alright, let's now import the `VillainDetailComponent` so we can use it:
`import {VillainDetailComponent} from './villain-detail.component';`
Add the element tag to the template:
`<my-villain-detail></my-villain-detail>`
Note that we have called it "my-villain-detail"; more about that later.
Bind the `villain` property in `AppComponent` to the `VillainDetailComponent`. We discussed doing that above; now let's do it! The updated `AppComponent` should look this:
### app.component.ts (adding villain-detail to template)
```
template:`
  <h1>{{title}}</h1>
  <h2>My Villains</h2>
  <ul class="villains">
    <li *ngFor="#villain of villains"
      [class.selected]="villain === selectedVillain"
      (click)="onSelect(villain)">
      <span class="badge">{{villain.id}}</span> {{villain.name}}
    </li>
  </ul>
  <my-villain-detail [villain]="selectedVillain"></my-villain-detail>
`,
```
The binding links villain from `AppComponent` to `VillainDetailComponent` and should update the detail every time a user selects a villain.
__But it doesn't work.__
Try it. Click a villain. See it not work. Look for an error in the console (you won't find one). Wut?
It is as if Angular is ignoring the new tag. Because *Angular is ignoring the new tab*.
The browser ignores HTML tags it doesn't recognize (which is good; Angular relies on this behavior). Cut from the same cloth, Angular also quietly ignores tags it does not recognize.
We need to inform Angular about the `VillainDetailComponent` by adding it to the `directives` array of `@Component` configuration metadata.
### Adding components to the *directives* array
Add the `directives` property to the bottom of the `@Component` configuration after the `styles` property.
`directives: [VillainDetailComponent]`
And... It works!
We;ve created a `VillainDetailComponent` that can be used anywhere to show villain details. It's a reuseable component.
## App Structure Review
After our refactoring, we should have a file structure like this:
```
angular2-tour-of-villains
├─ node_modules
├─ app
│   ├─ app.components.ts
│   └─ villain.ts
│   └─ villain-detail.component.ts
│   └─ main.ts
├─ index.html
├─ package.json
└─ tsconfig.json
```
## The Villainy We've Perpetrated
To summarize, we've:
1. Created a reuseable component
2. Learned one way to make a component accept input
3. Learned to bind parent and child components
4. Learned how to bring it all together by declaring needed components in a `directives` array

## Next Steps: Real Live Villainy
We are using shared components, but are still hard-coding our villain data. That's not gonna work in the Real World™. We really need a separate service. Let's do that [next](angular-2-services.md).
