# ngGuidelines

*Angular guidelines and tooling*

One of the purpose is to describe global guidelines to follow for Web Applications using Angular.js.
But it is also made to describe the entire flow of the application, from packaging to deployment.

*Inspired by [John Papa](https://github.com/johnpapa/angularjs-styleguide)* 

## Table of Contents

  1. [Application Structure](#application-structure)
  2. [Modules](#modules)
  3. [Injections](#injections)
  4. [Controllers](#controllers)

## Application Structure

When talking about Application Structure, there is multiple possible approach.

The chosen one is the *Folder By Features* over *Folder By Type*.

*Reasons* : 

- Easier to locate files 
- Purpose of each part of the application becomes obvious
- Make the application more scalable
- Easier to maintain (Add, remove, move feature)

Few features are default to each application:
- Blocks : Will contain common services and configuration
- Components : Will contain common directives
- Core : Contains shared constants and filters
- Layout : Will contain the main application files

```javascript
    app/
        app.module.js
        app.config.js
        app.routes.js
        blocks/
			exceptions.module.js
			exceptions.config.js
        components/
            my.directive.js
            my.directive.html
        core/
        	constants.js
        	filters.js
        layout/
            index.html
            index.controller.js
        
```					

## Modules

### Description 

Modules represent features, for each feature a module lives.

The main application module (app.module) has to remain small and will mainly hold all modules together.
From that point, the app.module will represent the manifest of all features of the application.

Each feature should specify on which features it rely.
It make them reusable out of the context of the app.module.

	> Not mandatory for small applications.
	
### Example

```javascript

// Main application module
angular
	.module('app', [
		'app.core',
		'app.components',
		'app.layout'
	]);
	
// Core application module
angular
	.module('app.core', [
		'app.blocks'
	]);

//Feature application module
angular
	.module('app.layout', [
		'app.core',
		'app.blocks'
	]);

```

## Injections

There are three different way to inject dependencies in angular:

### Implicit

Angular resolves dependencies based on the name of parameters.

```javascript
angular
	.module('app.layout')
	.controller('MyController', myController);

function myController(myService) {}
```

Can be problematic after minification, parameters will be renamed like 'a' and will break the resolution of the dependencies.

### Injection list

Specify on the controller function the list of modules.

```javascript
angular
	.module('app.layout')
	.controller('MyController, ["myService", myController]);
	
	function myController(myService) {}
```

That approach is minification safe, but there is still room for improvement.
It makes it difficult to detect which dependencies are injected.

### Property annotation $inject 

Specify all dependencies to inject through the $inject property of the controller function.

```javascript
angular
	.module('app.layout')
	.controller('MyController', myController);
	
	myController.$inject = ['myService'];
	function myController (myService) {}
```

This approach is minification safe and keeps everything clear.
Another advantage is that it mimmics the way [ngAnnotate](#ngAnnotate) works.

## Controllers

### Description

Controllers have to stay small and focused.
All logic not related to that will be moved to services.

### Example

```javascript

```

## Tooling
 - Node.js
 - Bower.js
 - Grunt.js
 - jsHint (Is this really a tool ?)
 - ngAnnotate (In grunt ?)

