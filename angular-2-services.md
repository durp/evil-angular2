Angular 2 Services of Doom!
===========================

In which we create a service to manage our Villain data.

## Services
The Tour of Villains is mutating and evolving; we foresee adding more components in the near future.
Multiple components will need access to Villain data and copying and pasting code all over the place is a terrible idea. Instead, let's create a single reuseable data service and inject it into the components that need it.
Refactoring data access to a separate service keeps the component lean and focused on supporting the view. It also makes it easier to unit test the component with a mock service.
Because the best data services are asynchronous, we'll ice the cake with a promise-based version of the data service.

## Where Were We?
The current folder structure ought to look this. If not, you have failed.
```
angular-2-tour-of-villains
├─ app
│   ├─ app.components.ts
│   └─ villain.ts
│   └─ villain-detail.component.ts
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
## Creating a Villain Service
Let's ditch the hard-coded villain list and start managing our villain data like responsible people.
### Create the VilllainService
Create an file in the `app` folder called `villain.service.ts`
> We've adopted a convention in which we spell the name of a service in lowercase followed by .service. If the service name is multi-word, we'd separate each word with dashes (kebab-case). So `SpecialSuperVillainService` would be encoded into the `special-super-villain.service.ts` file.

We name the class `VillainService` and export it for others to import.
### villain.service.ts
```
import {Injectable} from 'angular2/core';

@Injectable()
export class VillainService {
}
```

### Injectable Services
We imported and applied the Angular `Injectable` function as the `@Injectable()` decorator.
__Don't forget the parentheses!! Neglecting them makes Angular mad. And you won't like Angular when it's mad....__
TypeScript sees the `@Injectable()` and emits metadata about our service that Angular may need to inject other dependencies into this service. __Again: Stupid things will happen if you leave off the parens__
`VillainService` has no dependencies *at the moment*. Add the decorator anyway. It is "best practice" to apply `@Injectable()` from the start for both consistency's and future-proofing's sake.
### Getting Villianous
Add a `getVillains` method stub.
### villain.service.ts (getVillains stub)
```
@Injectable()
export class VillainService {
  getVillains() {

  }
}
```
We left `getVillains` stubbed to make an important point.
The consumer of our service doesn't know how the service gets the data. Data could come from a web service, local storage, or the very bowels of the earth. By extracting this service, we have insulated the client from the implementation and can change it as often as we like, for any reason, without touching any components that use villain data.
### Mock Villains
We already had mock `Villain` data hard-coded into `AppComponent`. It really belongs in its own file.
In `AppComponent`, change the `villains` reference to be an array of type `Villain`.

### app.component.ts
  public villains = Villain[];

Now let's remove the `VILLAINS` array from `AppComponent` and paste it into a new file called `mock-villains.ts`. We also need to add the `Villain` interface import as well because `VILLAINS` is an array of `Villain` type and is dependent on the `Villain` definition
### mock-villains.ts
```
import {Villain} from './villain'

export var VILLAINS: Villain[] = [
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
Notice we have added the `export` of the `VILLAINS` constant so it is exposed for use elsewhere - such as in `VillainService`.

## Return Mocked Villains
In the `VillainService`, let's import the mocked `VILLAINS` and return it from the `getVillains` method. Our `VillainService` should now look like this:
### villain.service.ts (mock villains)
```
import {Injectable} from 'angular2/core';
import {VILLAINS} from './mock-villains';

@Injectable()
export class VillainService {
  getVillains() {
    return VILLAINS;
  }
}
```

## Use the Villain Service!
We're ready to use the `VillainService` in other components. Let's begin with `AppComponent` by, as usual, importing the thing we want to use.
### app.component.ts (import VillainService)
`import {VillainService} from './villain.service'`;
Now `VillainService` is available within `AppComponent`. But how do we get a a reference to an instance of this service? We *do not* `new VillainService()`. No. Never. Bad. Remember all the trouble we went through to mark this thing `@Injectable()`? Let's put it to good use and inject an instance of this bad boy.

### Inject the VillainService
This is a two step process:
1. Add a constructor
2. Add to the component's `providers` metadata

Here's the constructor
### app.components.ts :: constructor
```
constructor(private _villainService: VillainService) { }
```
The constructor itself does nothing. The parameter simultaneously defines a private `_villainService` property and identifies it as a `VillainService` injection site.
> We prefix private variables with an underscore (\_) to warn those who would be so foolish as to attempt to read our code that this variable is *not* part of the components public API.

Now Angular will know that an AppComponent needs an instance of VillainService.

### But from whence shall Angular Inject?
The Angular *Injector* holds a collection of previously created service instances. Either it finds and returns a pre-existing `VillainService` or it creates a new instance, adds it to the collection, and returns the new instance to Angular.
However, the *Injector* does not yet understand how to create the new instance. If we run now (try it :) ), we will get an error:
```
EXCEPTION: No provider for VillainService! (AppComponent -> VillainService)
```
How to fix this? Easy... add an `provider` for `VillainService` in our app component metadata.
###app.component.ts :: adding a VillainService provider
```
providers: [VillainService]
```
Adding the `VillainService` as a member of the `providers` array instructs Angular to create a fresh instance of the `VillainService` when it creates a new `AppComponent`. The `AppComponent` can now use this service to get villains, as can every child component on its component tree.
> **Services and the component tree**
> Child components can be created by adding the child component selector tag to a template. In this example, `VillainDetail` is a child of `AppComponent`.
> If `VillainDetail` needed for some reason to use `VillainService`, it would, perforce, beed to ask Angular to inject an instance by defining an injection site in the constructor.
> However, the child component **must not** repeat its parents `providers` array. Why? Because it would be a *new* shadow instance of `VillainService`... and while shadow instances sound very villainous, they are also a bug waiting to happen. And everyone, hero and villain alike, hates bugs - its the one thing they can agree on.

### Let's get some Villains!
Now that we have an instance of `VillainService` injected and ready to rumble, let's use it.
We could just grab villains and shove them into a `villains` property, but let's not. instead let's use a getter. Why? **BECAUSE I SAID SO**
###app.component.ts :: getVillains
```
getVillains() {
  this.villains = this._villainService.getVillains()
}
```

### The *ngOnInit* Lifecycle Hook
Years of experience and exile in the Phantom Zone have taught us that doing things inside a constructor is a **Bad Idea™**. So... if we want to call `getVillains` as part of component creation, we *don't* want to do it in the constructor. Instead, let's lean on Angular and its [lifecycle hooks](https://angular.io/docs/ts/latest/guide/lifecycle-hooks.html).
The hook we are interested in is the 'at creation time' `OnInit` hook. Putting it in place is just a wee bit of configuration: we must import and implement the `OnInit` interface, which also means that we will write our own `ngOnInit` function.
###app.component.ts :: OnInit interface
```
import {OnInit} from 'angular2/core';

export class AppComponent implements OnInit {
  ngOnInit() {
    this.getVillains();
  }
}
```
Now we should be cooking with *Evil Gas™*: we get a list of villains and a villain detail view when we click on a villain name... but there is one last fly in the ointment. Synchronicity. And I don't mean the [Police album from 1983](https://en.wikipedia.org/wiki/Synchronicity_%28The_Police_album%29).

## Async Services and Promises
We have implemented a synchronous service. This is ok for now, but what if we want o get our services from a remote server via http? Let's go ahead and put the plumbing in place to handle async calls... with a *promise*.

A **Promise** lets us define a function that will be called when a task is done. Let's do that now.

###hero.service.ts :: getVillains
```
getVillains() {
  return Promise.resolve(VILLAINS);
}
```
This is still fake. We are cheating and resolving the promise immediately. But from the `AppComponent` point of view, `getVillains` now returns a promise. It doesn't care that we are faking it - which means that if we start to actually use a remote server, we won't need to change `AppComponent` (well, that's glossing over error handling, but what do you want? This is a tutorial!!)

So now update `AppComponent` to be promise-aware.
###app.component.ts :: Villains... as promised
```
getVillains() {
  this._villainService.getVillains().then(villains => this.villains = villains);
}
```

### What is '=>'?
Good question. That's the [ES2015 arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions). You could just use the old anonymous `function` expression, but the arrow function handles `this` gracefully. And if you have ever mishandled `this`, you'll appreciate it. Plus it looks cool.

App still working, right? Cool.

## What did we learn?
1. We created a shareable service class
2. We defined `VillainService` as a provider for `AppComponent`
3. We hooked into the Angular lifecycle via `ngOnInit`
4. We promised our `AppComponent` data and fulfilled (resolved) that promise

## What's next?

Routing and navigation between views. TBD.
