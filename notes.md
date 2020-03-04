 
## Setup
ng new my-app --routing

cd my-app
ng serve --port 3000  // start a development server 

ng build --prod // creates a build for deployment (static html, css, js)

In project root directory, run http-server dist/build-proj to serve those static files. Also host that directory on Heroku/AWS

ng lint , ng test 

---

## Components
Components - building blocks of UI. Composed of HTML template and TS code that consists of named imports the class needs, a class decorator and class logic. A component can be rendered using its selector. The @Component decorator designates the class as an Angular service

### Class variables 
Variables are declared in the root level of the class. They are assigned values inside methods, such as the constructor. Only class variables can be used in the template. Method variables are too nested for the template to access. 

### Passing data into components (parent => child communication)
Inline way:
Component selectors can be passed data as attributes. In Parent:
    <app-hello-world name="MyNAme"></app-hello-world>   // When rendering hello-world componentName
  In class HelloWorldComponent (Child),
    @Input('name') userName: string;  // username recieves its value from the selector data attribute. The class can then use this.username 

Create a model class (data transfer object) to serve as the template 
 in user.model.ts;
    export class User {
      name: string;
      title: string;
      address: string;
      phone: string[];
    }
...

In Parent.TS
    import { User } from './user.model';
    ...
    export class AppComponent {
      user: User;

      constructor() {     //-> normally this assignment work would be done in an api call
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
In Parent.html - pass in memory variable user and set it to user
    <hello-world [user]="user"></hello-world>

...

In Child.TS
    import {Component, OnInit, Input} from '@angular/core';
    import { User } from './user.model';

    export class HelloWorldComponent implements OnInit {
      @Input('user') user: User;

      constructor() { }

      ngOnInit(): void { }
    }

In Child.html 
    <div>
      <h1>{{ user.name }}</h1>
      <h3>{{ user.title }}</h3>
      <p>{{ user.address }}</p>
      <div *ngIf="user.phone.length > 0">
        <p>Phone:</p>
        <p *ngFor="let phone of user.phone">{{ phone }}</p>
      </div>
    </div>

  Alternate syntax for checking if a value is there before rendering: 
  Assume we have a class variable response that gets data after an api response returns
    <p>Login name: {{response?.login}}</p>

### Lifecycle Hooks 
    constructor() - fires first, before any component inputs initialize
    ngOnChanges() - fires when component inputs initialize, before ngOnInit()
    ngOnInit()  - fires when component fully mounts
    ngOnDestroy() - last to fire, used for cleanup work (remove any data feed substriptions)

---

## Templates

### Template Styles
Template styling for a component are specific to that component (style encapsulation). Template elements of a component are given a unique property, and component styles target those elements with those properties (using attribute selectors). Its good to insulate a component's styles from affecting other components. This is missing in React. 

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
Call the event APIs of Angular instead of the DOM. 
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
 Import forms module into app.module or the module with the component using 2-way binding;
    import { FormsModule } from '@angular/forms';
    ...
      imports: [
        BrowserModule,
        FormsModule
      ]

  In component class:
    inputText: string = "Initial Value";

  In template:
  One-Way data binding (component to view) would be:
    <input type="text" [value]="inputText" />

  For two-way data-binding, we use ngModel. The () indicates data flow from view to component, the [] indicates flow from component to view
    <input type="text" [(ngModel)]="inputText" />
    {{ inputText }}   // ==> shows that view updates the class variable as well

---

## Modules 
Modules - organize code. Contains related components and services (groups functionality). A module imports the different decorators used by the module and the class components, the directives, any child modules, and the (exported) components that are grouped into the module. 

The declarations array lists all the components, the imports array lists all the directives and child modules, providers array lists all services, and the bootstrap array lists the component to be rendered first. Only the app module needs to list the bootstrap. App module must import browser module.

Each component directory can have its own module. Child modules must import CommonModule, and export their components

1) Child Module must export its components
2) Parent Module must import Child Module

### Multiple Modules
Create a module for each feature. Each module can wrap its own components 
    `ng g module login`
create a component for that module
    `ng g component login/login-page`

In LoginModule;
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { LoginPageComponent } from './login.component';

    @NgModule({
      declarations: [
        LoginComponent
      ],
      imports: [
        CommonModule
      ],
      providers: [],
      exports: [
        LoginComponent
      ]
    })
    export class LoginModule { }

AppModule needs to import LoginModule and include it in the imports[]

## Services
Services - For reusable functionality thats used by many components, and code that interfaces with serverside APIs
    ng generate service serviceName
  This generates the service at the root level of the app directory
The @Injectible decorator designates the class as an Angular service
The module needing the service will have to import it and list it in the providers[]
Once a module uses a service, other modules touching that module automaticaly get that service; Angular creates a shared dependancy injection context. Its best practice to add the service to the module which most uses it. 

To use a service, inject it as a dependancy into the component (typecast it to a constructor parameter). Angular will look to see which constructor parameters are injectible - and will treat them as services. 

    export class TestServiceService {
      constructor() {}

      printToConsole(arg) {
        console.log(arg);
      }
    } 

    export class AppComponent {
      constructor(public svc: TestServiceService) {
        // use whatever service class methods
        this.svc.printToConsole("Got the Service");
      }

      ngOnInit() {
        this.svc.printToConsole("We Mounted!!");
      }
    }


    ----

  Creating interfaces to match incomming serverside declarations
  In the components directory, create a ts file for the interface;
    export interface PostObj {
      userId: number;
      id: number;
      title: string;
      body: string;
    }

  In the component's ts file, impor the interface and use it to typecast the class variables recieving serverside data  

    import { Component, OnInit } from "@angular/core";
    import { HttpClient } from "@angular/common/http";
    import { PostObj } from "./testPost";

    @Component({
      selector: "app-test",
      templateUrl: "./test.component.html",
      styleUrls: ["./test.component.css"]
    })
    export class TestComponent implements OnInit {
      apiResponse: Array<PostObj>;
      inputText: string;
      filteredList: Array<PostObj>;

      constructor(private http: HttpClient) {}

      ngOnInit(): void {
        this.http
          .get("https://jsonplaceholder.typicode.com/posts")
          .subscribe((res: Array<PostObj>) => {
            console.log("Response from server:", res);
            this.apiResponse = res;
          });
      }

      search() {
        console.log(this.inputText);
        console.log("API Response: ", this.apiResponse);

        this.filteredList = this.apiResponse.filter(
          (post: PostObj) =>
            post.title.includes(this.inputText) ||
            post.body.includes(this.inputText)
        );

        console.log("Filtered List: ", this.filteredList);
      }
    }

## REST calls 
Angular bundles fetch functionality into its HttpClient service. Once imported into a module (such as AppModule), all the other modules can access its functionality. Its best to add core Angular services to AppModule. 

In AppModule,
    import {HttpClientModule} from '@angular/common/http' and add HttpClientModule to imports[]
In the component making fetch calls, inject the HttpClient service dependency into the constructor 
    import {HttpClient} from '@angular/common/http'
    constructor(public http: HttpClient) {}

fetches are usually made upon component mounting
    ngOnInit() {
      //==> setup a subscription to observe for incoming data
      const obs = this.http.get("https://jsonplaceholder.typicode.com/todos/1");   
      //==> callback to observer.subscribe() fires when the (async) observer resolves with the data
      obs.subscribe(() => console.log("Got the response"));     
    }

## Routing

`ng new javabrains --routing`
You should have isolation of responsibilities and each view should have its own root component with its own sub components
Routes - reload parts of the screen dynamically by controlling url. As the URL is changes, the respoctive component tree is rendered. 

Define route URLs in app-routing.module.ts
First import the components. 
The routes are specified in the form of:  {path: 'url route', component: HomeComponent}
Configure Angular to map route URLs to components
const routes: Routes = [
  { path: "", redirectTo: "/home", pathMatch: "full" },
  { path: "home", component: HomeComponent },
  { path: "settings", component: SettingsComponent },
  { path: "**", component: PageNotFoundComponent }
];

The first is a redirct to come in case no endpoint is entered. The middle two map a user entered url to a component. The last is a wildcard that maps any non-existant route (and sub routes) to the PageNotFoundComponent

Upon accessing any of these url routes, angular injects the component into <router outlet> </router-outlet> which is rendered in app.html - which is the root component that contains all child components

Create Angular components for each view (one view for each route)

To specify child routes (routes appended to parent route in url bar), in app-routing module;

const routes: Routes = [ 
  ...
    {
      path: "settings",
      component: SettingsComponent,
      children: [
        { path: "", redirectTo: "profile", pathMatch: "full" },
        { path: "profile", component: SettingsProfileComponent },
        { path: "contact", component: SettingsContactComponent },
        { path: "**", redirectTo: "profile", pathMatch: "full" }
      ]
    },
  ...
]

(first import the child routes in app-routing module)

Also, the parent of the child components must have `<router-outlet></router-outlet>` in its template to specify the nested router outlet

### Link to routes in template 
Use the routerLink directive to avoud page refreshes
<a routerLink="home">Home</a>
<a routerLink="settings">Settings</a>

### Display route links dynamically 
In app.ts; 

    export class AppComponent {
      routes = [
        {linkName: 'Home', url: 'home'},
        {linkName: 'Settings', url: 'settings'}
      ]
    }

  In app.html 
    <a *ngFor="let route of routes" [routerLink]="route.url">{{route.linkName}}</a>