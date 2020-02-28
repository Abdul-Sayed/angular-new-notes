 
ng new my-app --routing

cd my-app
ng serve --port 3000  // serve the app 

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

### Class variables 
Variables are declared in the root level of the class. They are assigned values inside methods, such as the constructor. 

### Passing data into components (parent => child communication)
Inline way:
Component selectors can be passed data as attributes
    <app-hello-world name="MyNAme"></app-hello-world>   // When rendering hello-world componentName
  In class HelloWorldComponent,
    @Input('name') userName: string;  // username recieves its value from the selector data attribute. The class can then use this.username 

Create a model class (data transfer object) to serve as the template 
 in user.model.ts;
    export class User {
      name: string;
      title: string;
      address: string;
      phone: string[];
    }
in parent.TS
    import { User } from './hello-world/user.model';
    ...
    export class AppComponent {
      user: User;

      constructor() {
        this.user = new User();

        this.user.name = 'Goo Fu';
        this.user.title = 'Software Developer';
        this.user.address = '1234 Main St, Flushing , NYC, 1010101';
        this.user.phone = [
          '123-1234-123',
          '9513739047'
        ];

      }
    }
in parent.html 
    <hello-world [user]="user"></hello-world>

in child.TS
    import {Component, onInit, Input} from '@angular/core';
    export class HelloWorldComponent implements OnInit {
      @Input('user') user: User;

      constructor() { }

      ngOnInit(): void { }
    }
in child.html 
    <div>
      <h1>{{ user.name }}</h1>
      <h3>{{ user.title }}</h3>
      <p>{{ user.address }}</p>
      <div *ngIf="user.phone.length > 0">
        <p>Phone:</p>
        <p *ngFor="let phone of user.phone">{{ phone }}</p>
      </div>
    </div>

### Lifecycle Hooks 
    constructor() - fires first, before any component inputs initialize
    ngOnChanges() - fires when component inputs initialize, before ngOnInit()
    ngOnInit()  - fires when component fully mounts



## Templates

### Template Styles
Template styling for a component are specific to that component (style encapsulation). Template elements of a component are given a unique property, and component styles target those elements with those properties (using attribute selectors). 

For global styles accross all components, use the styles.css in the src  root directory
### Template Interpolation 
Any compoenent variables or methods can be evaluated in the template with {{ ..any nomal JS expression here.. }} 
Interpolated expressions are re-evaluated anytime the variables inside change - causing a UI update. 
### ngIf 
Selectively render/remove DOM elements 
    <div *ngIf="user.phone.length > 0">
      <p>Phone:</p>
      <p *ngFor="let phone of user.phone">{{ phone }}</p>
    </div>
### ngFor 
Loop through an array with ngFor
     <p *ngFor="let phone of user.phone">{{ phone }} </p>

### Event Handling 
(event)="classMethod()"  the () indicates data flowing from view to component
    <button (click)="toggleCollapse()">Expand/Collapse</button>
    <div *ngIf="!isCollapsed">
      ... some content
    </div>
In ts file
    isCollapsed = true;

    constructor() { }
    ngOnInit(): void { }

    toggleCollapse() {
      this.isCollapsed = !this.isCollapsed;
    }

### 2-way data binding (forms)
 Import forms module into app.module;
    import { FormsModule } from '@angular/forms';
    ...
      imports: [
        BrowserModule,
        FormsModule
      ],
  In component class:
    inputText: string = "Initial Value";
  In template:
  In ngModel, the () indicates data flow from view to component, the [] indicates flow from component to view
    <input type="text" [(ngModel)]="inputText" />
    {{ inputText }}   // ==> shows that view updates the class variable as well