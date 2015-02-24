# ngGuidelines

*Angular guidelines and tooling*

One of the purpose is to describe global guidelines to follow for Web Applications using Angular.js.
But it is also made to describe the entire flow of the application, from packaging to deployment.

## Table of Contents

  1. [Application Structure](#application-structure)
  2. [Tooling](#Tooling)

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

## Tooling
 - Node.js
 - Bower.js
 - Grunt.js
 - jsHint (Is this really a tool ?)
 - ngAnnotate (In grunt ?)

