Angular 2 in 5.25 Minutes
======================

Adapted for your amusement from [5 Min Quickstart](https://angular.io/docs/ts/latest/quickstart.html)

1. Set up  development environment
  * Create a new project folder (don't call it '<your project name>', that's a placeholder)
    * `mkdir <your project name>`
  * Add the libraries we need
    * npm -> `brew install -g npm`
  * Add a `package.json`
    * -> `npm init`
    * Add dependencies to [package.json](#packagejson)
  * Configure typescript
    * Add a [tsconfig.json](#tsconfigjson)
    * Add a [typings.json](#typingsjson)
  * Create an application source sub-folder and
    * `mkdir app; cd app`
2. Write the Angular root [component](#the-component-class) for our app
  * Add the component file [app.components.ts](#appcomponentsts)
3. Bootstrap!
  * Add the [main.ts](#maints) to the `app` folder
4. Add the [index.html](#indexhtml) to the root (not in `app`!)
5. Compile and run
  * `npm start`


## Final structure
If you followed the steps above, your project should look like this:
```
<your project name>
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

# What Now?
All started up? Read some more detail about things below, or start the [Tour of Villains](angular-2-tour-of-villains.md).


# The Component class
At the bottom of the file is am empty class `AppComponent` what will hold our properties and application logic.

## `export`
The `export` command transforms this file into a module. Additional child components, files and modules will be added to create a more sophisticated application.

## `import`
The `import` command allows us to use methods and objects defined in other modules. We need the `angular2/core`, so we import it in the first line of the file.

## Decorating with the `@` symbol
TypeScript allows us to apply functions to classes via decoration, we can define an Angular component by passing a `selector` and  `template` to the `@Component` method.

For example, if we define a component like this:
```
@Component({
    selector: 'my-app',
    template: '<h1>My First Angular 2 App</h1>'
})
```
Then Angular will create our an instance of our component and render our `template` any time it encounters our `selector` in the host html.

### `selector`
The `selector` property is a plain old CSS selector. Angular will create and display an instance of our `AppComponent` wherever the `my-app` element is found in the html.

### `template`
The `template` property holds the html used to render the view.


#package.json
```
{
  "name": "<your project name>",
  "version": "1.0.0",
  "scripts": {
    "start": "concurrent \"npm run tsc:w\" \"npm run lite\" ",
    "tsc": "tsc",
    "tsc:w": "tsc -w",
    "lite": "lite-server",
    "typings": "typings",
    "postinstall": "typings install"
  },
  "license": "ISC",
  "dependencies": {
    "angular2": "2.0.0-beta.7",
    "systemjs": "0.19.22",
    "es6-promise": "^3.0.2",
    "es6-shim": "^0.33.3",
    "reflect-metadata": "0.1.2",
    "rxjs": "5.0.0-beta.2",
    "zone.js": "0.5.15"
  },
  "devDependencies": {
    "concurrently": "^2.0.0",
    "lite-server": "^2.1.0",
    "typescript": "^1.8.2",
    "typings": "^0.6.8"
  }
}
```

#tsconfig.json
```
{
  "compilerOptions": {
    "target": "ES5",
    "module": "system",
    "moduleResolution": "node",
    "sourceMap": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": false,
    "noImplicitAny": false
  },
  "exclude": [
    "node_modules",
    "typings/main",
    "typings/main.d.ts"
  ]
}
```

#typings.json
```
{
  "ambientDependencies": {
    "es6-shim" : "github:DefinitelyTyped/DefinitelyTyped/es6-shim/es6-shim.d.ts#4de74cb527395c13ba20b438c3a7a419ad931f1c"
  }
}
```

#app.components.ts
```
import {Component} from 'angular2/core';

@Component({
    selector: 'my-app',
    template: '<h1>My First Angular 2 App</h1>'
})
export class AppComponent { }
```

#main.ts
```
import {bootstrap}    from 'angular2/platform/browser'
import {AppComponent} from './app.component'

bootstrap(AppComponent);
```

#index.html
```
<html>

  <head>
    <title>Angular 2 QuickStart</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">    
    <link rel="stylesheet" href="styles.css">

    <!-- 1. Load libraries -->
    <!-- IE required polyfills, in this exact order -->
    <script src="node_modules/es6-shim/es6-shim.min.js"></script>
    <script src="node_modules/systemjs/dist/system-polyfills.js"></script>
    <script src="node_modules/angular2/es6/dev/src/testing/shims_for_IE.js"></script>

    <script src="node_modules/angular2/bundles/angular2-polyfills.js"></script>
    <script src="node_modules/systemjs/dist/system.src.js"></script>
    <script src="node_modules/rxjs/bundles/Rx.js"></script>
    <script src="node_modules/angular2/bundles/angular2.dev.js"></script>

    <!-- 2. Configure SystemJS -->
    <script>
      System.config({
        packages: {        
          app: {
            format: 'register',
            defaultExtension: 'js'
          }
        }
      });
      System.import('app/main')
            .then(null, console.error.bind(console));
    </script>

  </head>

  <!-- 3. Display the application -->
  <body>
    <my-app>Loading...</my-app>
  </body>

</html>
```
