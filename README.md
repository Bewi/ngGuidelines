# ngGuidelines

*Angular guidelines and tooling*

One of the purpose is to describe global guidelines to follow for Web Applications using Angular.js.
But it is also made to describe the entire flow of the application, from creation to deployment.

*Inspired by [John Papa](https://github.com/johnpapa/angularjs-styleguide)* 

## Table of Contents

  1. [Application Structure](#application-structure)
  2. [Naming Conventions](#naming-conventions)
  3. [Modules](#modules)
  4. [Injections](#injections)
  5. [Controllers](#controllers)

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

[Back to top](#ngguidelines)

## Naming conventions

### Files
Files will use snake-casing for the name, separted by a dot for the type and language to look like :

	my-name.type.js

- Modules: my-feature-name.module.js
- Services & Factories: my-service-name.service.js
- Controllers: my-controller-name.controller.js 
- Directives: my-directive-name.directive.js
- Filters: my-feature.filters.js
- Constants: my-feature.constants.js
- Config: my-feature.config.js
- Routes: my-feature.routes.js

*Reasons:* It makes it easier to directly identify files by their name.

> Main application files will always represent the "app" feature.

> app.module.js, app.config.js, app.routes.js, ...

### In code

#### Modules

Module name will be named using camel-casing.
The name will be made of parent features separted by a dot.

```javascript

// Parent feature
app.module('app');

// Core feature which is child of the app feature
app.module('app.core');

// Layout feature which is child of the app feature
app.module('app.layout');

...

```

#### Controllers

Controller will be exceptionnaly named using pascal-casing.
Which is a standard for object that can be instantied through a constructor.

Controllers will have a "Controller" suffix to make it more explicitly descriptive.

```javascript

app
	.module('app.layout')
	.controller('LayoutController', LayoutController);
	
	function LayoutController() { ... }

```

#### Factories & Services

Factories & Services will be camel-cased.

```javascript

app
	.module('app.core')
	.factory('logger', logger);

function logger () { ... }

```

#### Directives

Directives will be camel-cased.

An area prefix will be used to describe where the directive belongs.

*Reasons:*

- It make it easier to determine where does the directive come from.
- Avoid conflict.

```javascript

app
	.module('app.components')
	.directive('xxMyDirective', xxMyDirective);
	
funtion xxMyDirective () { ... }
	
```

#### Constants

Constants will remain camel-cased and will have to describe explicitly the purpose of the constant.

If constant value is an object, every property of that object has to be ALL_CAPS cased.

```javascript 

app
	.module('app')
	.constant('simpleConstant', 10)
	.constant('complexConstant', {
		PROPERTY_ONE: 'value one',
		PROPERTY_TWO: 'value two'
	});

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

There are three differents way to inject dependencies in angular:

### Implicit Annotation

Angular resolves dependencies based on the name of parameters.

```javascript
angular
	.module('app.layout')
	.controller('MyController', MyController);

function MyController(myService) {}
```

Can be problematic after minification, parameters will be renamed like 'a' and will break the resolution of the dependencies.

### Inline Array Annotation

Specify on the controller function the list of modules.

```javascript
angular
	.module('app.layout')
	.controller('MyController, ["myService", MyController]);
	
	function MyController(myService) {}
```

That approach is minification safe, but there is still room for improvement.
It makes it difficult to detect which dependencies are injected.

### $inject Array Annotation

Specify all dependencies to inject through the $inject property of the controller function.

```javascript
angular
	.module('app.layout')
	.controller('MyController', MyController);
	
	MyController.$inject = ['myService'];
	function MyController (myService) {}
```

This approach is minification safe and keeps everything clear.
Another advantage is that it mimmics the way [ngAnnotate](#ngAnnotate) works.

## Controllers

### Description

Controllers have to stay small and focused.
All logic not related to that will be moved to services.

### ControllerAs over $scope

ControllerAs approach of controller resolve a inheritence issue encoutered with $scope.
It allows a better syntax by specifying a namespace for each controller.
Still accessible from the $scope.myController (where myController is the name chosen).

```html
<div ng-controller="LayoutController As layout">
	<span>{{ layout.title }}</span>
</div>
```

```javascript

angular
	.module('app.layout')
	.controller('LayoutController', LayoutController);
	
LayoutController.$inject = ['$scope'];
function LayoutController ($scope) {
	this.title = "Controller as fever !";
	console.log($scope.layout === this); /// will return true
}

```

When working with *this* in javascript, it is always a safe choice to store it in a variable.
As *this* is contextual, it may not be the one expected depending on the context.
When doing so, it is a better choice to stay consitent with the name of he variable.
In this document it will be named 'me'.

```javascript

function layoutController ($scope) {
	var me = this;
	
	function accessMe () {
		me.value = "I'm the controller!";
	}
}

```


### Inheritance

Controllers don't inherit directly from each other.
Meaning a ChildController will not have the "value" property of his ParentController.
However ParentController is accessible within the ChildController area.

```html
<div ng-controller="ParentController As parent">
	<span>{{ parent.value }}</span>
	<div ng-controller="ChildController As child">
		<span>{{ parent.value }}
	</div>
</div>
```

```javascript
// Parent
angular
	.module('app.inheritance')
	.controller('ParentController', ParentController);
	
function ParentController () {
	var me = this;
	me.value = "I'm the parent";
}

// Child
angular
	.module('app.inheritance')
	.controller('ChildController', ChildController);
	
function ChildController () {
	var me = this;
	console.log(me.value); // will log undefined
}
```

Parent can be accessed from the Scope :

```javascript

$scope.parent.value; // where parent is the name given to the controller through the ControllerAs

```

Child can copy parent properties.
Has to be used carefully as ChildController cannot change parent and will not be notified of changes made on the parent

```javascript

function ChildController () {
	ParentController.apply(this, options);
}

```

## Tooling
 - Node.js
 - Bower.js
 - Grunt.js
 - jsHint (Is this really a tool ?)
 - ngAnnotate (In grunt ?)

