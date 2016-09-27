Angular 2 Moar Villains
==========================

# Making Villains from Scratch
Let's make an array of villains. It's easy. Anyone can do it. Go ahead and add the following to the end of the app.component.ts
### app.component.ts
```
var VILLAINS: Villain[] = [
  { "id": "a1", "name" : "He Who Must Not Be Jim" },
  { "id": "b2", "name" : "Bad Food Hollywood" },
  { "id": "c3", "name" : "Snake Coffin" },
  { "id": "d4", "name" : "Dark Griffin" },
  { "id": "e5", "name" : "Death Ray" },
  { "id": "f6", "name" : "Mad Dog Donaho" },
  { "id": "g7", "name" : "Mitch the Evil Intern" },
  { "id": "h8", "name" : "El Jefe" },
  { "id": "i9", "name" : "Dr. Skiptonite" },
  { "id": "ja", "name" : "... Tim" }
]
```
`VILLAINS` is an array of type `Villain` that represents our league of villains. We would fetch such data from a web service, but for right now, we are just mocking these villains (but not to their faces).

# Exposing Villains
Let's make a property in `AppComponent` to store these evildoers.
### app.component.ts
```
export class AppComponent {
  public villains = VILLAINS;
```
Notice that no type is required, TypeScript will infer it from the array. (Thanks TS!)
## Villains on display
Let's make an unordered (disordered?) list in our template. Insert this html chunk below the title and above the villain details.
### app.component.ts :: template
```
  <h2>My Villains</h2>
  <ul class="villains">
    <li>
      <-- insert villain here -->
    </li>
  </ul>
```
Ok, we have a basic template, but where are our villains? We need `*ngFor` to iterate over our array.
Let' enhance that simple <li> to make it seriously wicked.

### app.component.ts :: template
```
    <li *ngFor="#villain of villains">
```
__Don't forget the asterisk \*__
The asterisk tells Angular that the <li> and its children are the "repeater template"
__Don't forget the hash #__
The hash makes `villain` a local template variable

## Moar Content!
Let's insert some villainy and remove our placeholder.
### app.component.ts :: template
```
    <li *ngFor="#villain of villains">
      <span class="badge">{{villain.id}}</span> {{villain.name}}
    </li>
```
And lo! A legion of doom!
### Stylish Villainy
Seems pretty goof-nuts of us to put some CSS inline, but this looks super crappy right now. Let's just slap on a facade and pretend we don't know any better. I promise, this is not how we will do things in the future. Note the use of the back-ticks (\`), they are easy to miss...
### app.component.ts :: style
```
styles:[`
  .selected {
    background-color: #CFD8DC !important;
    color: white;
  }
  .villains {
    margin: 0 0 2em 0;
    list-style-type: none;
    padding: 0;
    width: 10em;
  }
  .villains li {
    cursor: pointer;
    position: relative;
    left: 0;
    background-color: #EEE;
    margin: .5em;
    padding: .3em 0em;
    height: 1.6em;
    border-radius: 4px;
  }
  .villains li.selected:hover {
    color: white;
  }
  .villains li:hover {
    color: #607D8B;
    background-color: #EEE;
    left: .1em;
  }
  .villains .text {
    position: relative;
    top: -3px;
  }
  .villains .badge {
    display: inline-block;
    font-size: small;
    color: white;
    padding: 0.8em 0.7em 0em 0.7em;
    background-color: #607D8B;
    line-height: 1em;
    position: relative;
    left: -1px;
    top: -4px;
    height: 1.8em;
    margin-right: .8em;
    border-radius: 4px 0px 0px 4px;
  }
`]
```
Whew. My clipboard is tired. Inline styles won't bleed out to other components, but we will probably want to move them out to their own file because inline styles are the devil's playthings. We are making a legion of evil though, so let's just smile wickedly and keep rolling.
## Selecting a Villain
Lists are boring. Let's do something to spice it up. We shall allow the use to click on a listed villain and get the villainous info in the details section. Let's bind a click event to the <li>.
### app.component.ts :: template
```
    <li *ngFor="#villain of villains" (click)="onSelect(villain)">
      <span class="badge">{{villain.id}}</span> {{villain.name}}
    </li>
```
The click handler `(click)="onSelect(villain)` is pretty straightforward. When a user clicks on this element, call the `onSelect` method, passing the local template variable `villain`. But we don't have an `onSelect` method yet. Or a place to store the reference to our selected villain. Let's add these missing things.
### app.component.ts :: template
```
public selectedVillain : Villain;

onSelect(villain : Villain) { this.selectedVillain = villain; }
```
Let's now bind the details in the detail section to the selectedVillain. Easy-peasy.
### app.component.ts :: (Binding to the selectedVillain)
```
<h2>{{selectedVillain.name}} details!</h2>
<div><label>id: </label>{{selectedVillain.id}}</div>
<div>
    <label>name: </label>
    <input [(ngModel)]="selectedVillain.name" placeholder="name"/>
</div>
```
But when there is no selection, this looks like crap. What do you do with crap? That's right, you hide it! Hide the crap with an `*ngIf`
### app.component.ts :: (Hiding the crap with an \*ngIf)
```
<div *ngIf="selectedVillain">
  <h2>{{selectedVillain...
    ...
</div>
```
Everything wrapped in the `ngIf` <div> will be excluded from the DOM and not be rendered unless `selectedVillain` is truthy. Awesome. Crap well-hidden. When the user clicks a villain, `selectedVillain` will be populated, and the villainous details will be revealed to the world. Muahahhahahah.
## Making Good Evil Selections
Our user experience could be better. It isn't obvious when a villain in the list is selected. Let's fix that via property binding.
### app.component.ts :: (making good evil selections)
```
    <li *ngFor="#villain of villains" [class.selected]="villain === selectedVillain" (click)="onSelect(villain)">
    ...
```
We are saying: “apply the selected class if the villains match, remove it if they don’t”. This means our selected villain gets the `selected` class and the associated styling.

# Sum of all Evil
Our current `app.component.ts` ought to look like this:
### app.component.ts :: dastardly summary
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
    <h2>My Villaine</h2>
    <ul class="villains">
      <li *ngFor="#villain of Villains"
        [class.selected]="villain === selectedVillain"
        (click)="onSelect(villain)">
        <span class="badge">{{Villain.id}}</span> {{villain.name}}
      </li>
    </ul>
    <div *ngIf="selectedVillain">
      <h2>{{selectedVillain.name}} details!</h2>
      <div><label>id: </label>{{selectedVillain.id}}</div>
      <div>
        <label>name: </label>
        <input [(ngModel)]="selectedVillain.name" placeholder="name"/>
      </div>
    </div>
  `,
  styles:[`
    .selected {
      background-color: #CFD8DC !important;
      color: white;
    }
    .villains {
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      width: 10em;
    }
    .villains li {
      cursor: pointer;
      position: relative;
      left: 0;
      background-color: #EEE;
      margin: .5em;
      padding: .3em 0em;
      height: 1.6em;
      border-radius: 4px;
    }
    .villains li.selected:hover {
      color: white;
    }
    .villains li:hover {
      color: #607D8B;
      background-color: #EEE;
      left: .1em;
    }
    .villains .text {
      position: relative;
      top: -3px;
    }
    .villains .badge {
      display: inline-block;
      font-size: small;
      color: white;
      padding: 0.8em 0.7em 0em 0.7em;
      background-color: #607D8B;
      line-height: 1em;
      position: relative;
      left: -1px;
      top: -4px;
      height: 1.8em;
      margin-right: .8em;
      border-radius: 4px 0px 0px 4px;
    }
  `]
})
export class AppComponent {
  public title = 'Tour of Villains';
  public villains = Villains;
  public selectedVillain : Villain;
  onSelect(villain: Villain) { this.selectedVillain = Villain; }
}
var VILLAINS: Villain[] = [
  { "id": "a1", "name" : "He Who Must Not Be Jim" },
  { "id": "b2", "name" : "Bad Food Hollywood" },
  { "id": "c3", "name" : "Snake Coffin" },
  { "id": "d4", "name" : "Dark Griffin" },
  { "id": "e5", "name" : "Death Ray" },
  { "id": "f6", "name" : "Mad Dog Donaho" },
  { "id": "g7", "name" : "Mitch the Evil Intern" },
  { "id": "h8", "name" : "El Jefe" },
  { "id": "i9", "name" : "Dr. Skiptonite" },
  { "id": "ja", "name" : "... Tim" }
];
```
## What did we learn?
1. How to list villains
2. How to click-select a villain
3. How to hide crap until it is not crap anymore
4. How to add classy styling

# What's next?
More villainy! Subcomponents! Divide and conquer in our next chapter [Components of Evil](angular-2-components-of-evil.md)
