
ng new my-app --routing

cd my-app
ng serve --open  // serve the app

ng build  // creates a build for deployment
ng lint , ng test  // other tests

ng g c componentName  -- generate a component. Add a -d flag to create a dry run and see what it makes

## Modules
Modules - organize code. Contains related components and services (groups functionality). A module imports the different decorators used by the module and the class components, the directives, any child modules, and the (exported) components that are grouped into the module. 

The imports array lists all the directives and child modules, the declarations array lists all the components, and the bootstrap array lists the component to be rendered first. Only the app module needs to list the bootstrap. App module must import browser module.

Each component directory can have its own module. Child modules must import CommonModule, and export their components

## Services
Services - reusable code, and code that connects to server where calculations are done

## Routes
Routes - reload parts of the screen dynamically by controlling url

## Components
Components - building blocks of UI. Composed of HTML template and TS code that consists of named imports the class needs, a class decorator and class logic. A component can be rendered using its selector. 

