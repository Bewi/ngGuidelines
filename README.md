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
  6. [Directives](#directives)
  7. [Routes](#routes)
  8. [Traductions](#traductions)

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
index.html
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
locales/
	en/
		app.en.json
	fr/
		app.fr.json
libs/
	angular.js
	...
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
- Translations: my-feature.language.json

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

While naming, '$' sign is prohibited.

*Reasons:*

- The '$' sign is reserved for angular services.
- Avoid conflicts with other services.

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
	
function xxMyDirective () { ... }
	
```

> To have a valid html, the directive name has to start with data-xx-my-directive (only on the HTML part)

> This practice has to be avoided to keep the html file more readable.

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

#### Translations 

Translations will be snake cased, to make them more readable inside html code.

```javascript

{
	'my-key': 'Some text.'
}

```

####

[Back to top](#ngguidelines)

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

[Back to top](#ngguidelines)

## Injections

When injecting dependencies, external dependencies have to be injected first.

*Reason*: It make it easier to detect dependencies on the list.

```javascript

MyController.$inject = ['$logger', '$i18next', 'myService'];

```

There are three differents ways to inject dependencies in angular:

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
Another advantage is that it mimmics the way [ngAnnotate](https://www.npmjs.com/package/grunt-ng-annotate) works.

[Back to top](#ngguidelines)

## Controllers

### Description

Controllers have to stay small and focused.
All logic not related to that will be moved to services.

Controllers NEVER manipulate the DOM, has to be done through directives link instead.

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

[Back to top](#ngguidelines)

## Directives

Directives are made to be reusable. 
They have to be as generic and flexible as possible.

A directive has no reason to be if all it does can be replaced by css.

### Initialisation

```javascript

app
	.module('app')
	.directive('xxxMyDirective', xxxMyDirective);

function xxxMyDirective () {
	return { ... };
}

```

### Restrictions

A directive can be restricted to be as AEC, respectivly attribute, element and class.

By default a directive is restricted to AE, so either an attribute or an element.

Class has to be avoided.

*Reasons:* 
- There is a high probability to have other classes on your element, which will make the directive to identify.
- It makes it difficult to determine if a class is a directive or not.

```javascript

function xxxMyDirective () {
	return { 
		restrict: 'EA' // Default
	};
}

```

##### When to choose an element or attribute ?

Use element when the directive manipulate his template.

Prefer attribute when the directive decorate an existing element.

### Templates

Prefer to use templateUrl instead of template, except if the template is one html element.

*Reason:* It make it easier to maintain an html file than an inline html code.

```javascript
// One tag template
function xxxMyDirective() {
	return {
		template: '<div></div>'
	};
}

// More complexe template
function xxxMyDirective() {
	return {
		templateUrl: 'myComplexTemplate'
	}
}

```

```html

<div>
	<span></span>
	<span></span>
</div>

```

### Controller As

To stay consistent, ControllerAs will be used for directives.

As controller can be required by other directives, only needed behavior will be exposed.

```javascript

function xxMyDirective() {
	return {
		...
		
		controller: MyDirectiveController,
		controllerAs: 'MyDirectiveController As vm'
	}
}

function myDirectiveController() { ... }

```

> Never manipulate the DOM directly in the controller, use the link function instead

### Isolated scope

Use isolated scope to avoid conflicts with outter scope and to hide unnecessary information to the outside.

By default isolated scope will not be bind to the controller.

The property bindToController can be set to true to fix this behavior.

```javascript

function xxMyDirective() {
	return {
		...
		
		controller: MyDirectiveController,
		controllerAs: 'MyDirectiveController As vm',
		bindToController: true,
		scope: { value: '=' }
	}
}

function MyDirectiveController() { 
	console.log(this.value); // Will output the initial value bind to the isolated scope.
}

```

### Link

Used to manipulate the DOM.

Controller is automatically injected into the link function.
But it can also be accessed through the scope.

```javascript

function MyDirectiveController() {
	return {
		link: link
	};
}

function link (scope, element, attrs, controller) {
	// Value is the same.
	console.log('scope.vm.value %s', scope.vm.value);
	console.log('controller.value %s', controller.value);
}

```

[Back to top](#ngguidelines)

### Routes

Route mapping will be achieved using [uiRouter](https://github.com/angular-ui/ui-router) library.

Global routes will be registered on application config (app.routes.js).

Modules will register their own routes.

#### Configuration

In the following example, the global configuration only specify the default view.
Where movies module register his own route.

*Reasons:*

- Each module becomes responsible of managing his routes.
- Main application doesn't have to know about sub module routes.
- Make it easier to add / remove a module.

To be consistent, controllerAs syntax has to be used.
When talking about routes, try as much as possible to use 'vm' as alias.

``` javascript

// app.config
angular
        .module('app')
        .config(configRoutes);

configRoutes.$inject = ['$urlRouterProvider'];
function configRoutes ($urlRouterProvider) {
	$urlRouterProvider.otherwise("/movies");
}

// movies.config
angular
    .module('app.movies')
    .config(configRoutes);

configRoutes.$inject = ['$stateProvider'];
function configRoutes($stateProvider){
    $stateProvider
        .state('movies', {
            url: '/movies',
            templateUrl: "app/movies/movies.html",
            controllerAs: "vm",
            controller: "MoviesController"
        });
}

```

#### Usage

In html code only 2 elements are needed, 'ui-view' directive for content element and 'ui-sref' for link element.

``` html

<a ui-sref="movies">Movies</a>
<div ui-view></div>

```

In controllers, $state service can be injected.
One advantage of that service is that it provide the current state, for exemple 'movies', which make it easier to use than an url.

``` javascript

// Verify if parameter is the current state.
function isRoute(r) {
    return $state.$current.toString() === r;
}

```

[Back to top](#ngguidelines)

## Traductions

Traductions will be achieved using i18next coupled with ng-i18next library.

All information about ng-i18next can be found [here](https://github.com/i18next/ng-i18next).

### Configuration

#### Basic

Basic configuration will be done on the configuration of the main app (app.config).

```javascript

$i18nextProvider.options = {
		// Keep language state in a cookie
        useCookie: true, 			
        // Keep translations in local storage
        useLocalStorage: false,
        // Unvalidate the local storage after one week (default)
        localStorageExpirationTime: 86400000,
        // fallback language if no translation for current language
        fallbackLng: 'en',
        // language
        lng: 'en',
        // Location of all translation files, where lng is the language and ns is the namespace
        resGetPath: 'locales/__lng__/__ns__.__lng__.json',
        // Value to display while loading translations
        defaultLoadingValue: '',
        // namespace configuration
        ns: {
        	// Specify here all namespaces
            namespaces: ['app'],
            // Specify the default namespace
            defaultNs: 'app'
        }
    };

```

#### Multiple namespaces

If a feature is defined by a module, a namespace will be added to the translator.
Otherwise translations will be added to "app" namespace.

A provider will be made to manage namespaces at config time.
Each module will add his own namespace.

From that point the app config doesn't have to manage namespaces, but only pass them to the configuration of i18next.

```javascript

// on Feature module config
function config(namespacesProvider) {
	namespacesProvider.addNamespace('feature');
}

// on App module config
function config($i18nextProvider, namespacesProvider) {
	namespacesProvider.addNamespace('app');

	$i18nextProvider.options = {
		...,
		ns {
			namespaces: namespacesProvider.getNamespaces(),
			defaultNs: 'app'
		}
}

```

### Usage

#### In html

There are two way to translate in html using ng-i18next.

Using a directive or a filter. Both are used in appropriate case.

Filter will be used for direct content of html element.

*Reasons:*
- It makes sense to keep the content where it will be shown.
- It keeps the html tag clean.

```html

<!-- Translate 'my-key' -->
<span>{ 'my-key' | i18next }</span>

<!-- Translate $scope.myKey -->
<span>{ myKey | i18next }</span>

```

Directives will be used for translation of html attribute.

*Reasons:*
- It is not possible to do it through the filter.
- It does not makes sense to have a parameter translation on the content of the html element.

```html

<!-- Translate the title attribute of 'a' tag -->
<a ng-i18next="[title]my-key"></a>

<!-- Compiled element if my-key translation it My title -->
<a title="My title"></a>

```

#### In javascript

The library ng-i18next provide a Service that can be use inside Angular code.

```javascript

MyController.$inject = ['$i18next'];
MyController($i18next) {
	var translation = $i18next('my-key');
}

```

> Has to be used only when really needed, prefer to use html way instead.


## Tooling
 - Node.js
 - Bower.js
 - Grunt.js
 - jsHint (Is this really a tool ?)
 - ngAnnotate (In grunt ?)

