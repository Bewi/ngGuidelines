# ngGuidelines

*Angular guidelines and tooling*

One of the purpose is to describe global guidelines to follow for Web Applications using Angular.js.
But it is also made to describe the entire flow of the application, from packaging to deployment.

*Inspired by [John Papa](https://github.com/johnpapa/angularjs-styleguide)* 

## Table of Contents

  1. [Application Structure](#application-structure)
  2. [Modules](#modules)

## Application Structure

When talking about Application Structure, there is multiple possible approach.

The chosen one is the *Folder By Features* over *Folder By Type*.

*Why ?* : 

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

## Tooling
 - Node.js
 - Bower.js
 - Grunt.js
 - jsHint (Is this really a tool ?)
 - ngAnnotate (In grunt ?)

