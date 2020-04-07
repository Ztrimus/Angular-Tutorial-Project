# Angular-Tutorial-Project

## Repeat following in practice
1. canActivate
2. canDeactivate
3. Resolver


# Sections
1. Component
2. Directives
3. Services
4. Routing

# 1. COMPONENT

#### 1.1. When you want to Communicate from Parent to Child Component through property or event binding
in Parent html
```html
<app-server-element *ngFor="let serverElement of serverElements" [element]="serverElement"></app-server-element>
```

in child ts
```js
@Input('srvElement') element: { name: string, type: string, content: string };
```

#### 1.2. When you want to Communicate from Child to Parent Component through property or event binding
in Parent html
```html
<app-cockpit (serveCreated)="onServerAdded($event)"></app-cockpit>
```

in child ts
```js
@Output() serveCreated = new EventEmitter<{serverName: string, serverContent: string}>();

onAddServer() {
this.serveCreated.emit({
    serverName : this.newServerName,
    serverContent: this.newServerContent
});
}
```

#### 1.3. Local Reference
Getting access to the element of HTML in TS

in html
```html
<input type="text" #serverInputElement>
<button (click)="onServerCreated(serverInputElement)">
```
"serverInputElement" is local Reference

in ts 
```js
onServerCreated(inputElement) { 
    console.log(inputElement.value);
}
```
#### 1.4. ViewChild()
get all property or attirbute of component, local reference many more
```js
@ViewChild('serverContent') serverContentInput: ElementRef;
```

#### 1.5. ng-content
By default, anything you written between your custom tags gets lost
e.g.
in parent.html
```html
<app-my-element>
    <p> This Line will be get lost.</p>
</app-my-element>
```
but you can change that behavior using ng-content
ng-content replace by everything you type within your custom tags.

in app-my-element.html
```html
<div>
    <ng-content></ng-content>
</div>
```
in parent.html
```html
<app-my-element>
    <p> This Line will be get lost.</p>
</app-my-element>
```

# 2. DIRECTIVES
1. Attirbute Directives
    a. ngClass
    b. ngStyle
2. Structural Directives
    a. ngIf
    b. ngFor

## 2.1. Attribute Directive

#### 2.1.1. Build a custom directive 
```bash
ng g d new-directive
```
#### 2.1.2. you can use directive's selector as an attribute in HTML elements
   e.g. 
   in directive.ts
   ```js
   import { Directive, ElementRef, OnInit } from '@angular/core';

    @Directive({
        selector: '[customDirective]',
    })
 ```
   in html
   ```html
   <app-custom-element customDirective></app-custom-element>
```

#### 2.1.3. ElementRef 
Can change property of attribute in ts
e.g. 
```ts
import { ElementRef } from '@angular/core';
constructor(private elementRef: ElementRef) { }
ngOnInit() {
    this.elementRef.nativeElement.style.background = 'green';
} 
```
**But this is not good way to change DOM property in TS.**

#### 2.1.4. Renderer : Better than previous one
```ts
import { Renderer2 } from '@angular/core';
constructor(private elRef: ElementRef, private renderer: Renderer2) { }
ngOnInit() {
   this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
}
````

#### 2.1.5. @HostListener
import it and load it in constructor
```ts
@HostListener('mouseenter') mouseover(eventData: Event) {
    this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
    }
```
#### 2.1.6. @HostBinding
import it and load it in constructor
```ts
@HostBinding('style.backgroundColor') backgroundColor = 'transparent';
@HostListener('mouseenter') mouseover(eventData: Event) {
    this.backgroundColor = 'yellow';
}
```
#### 2.1.7. Using Directive's variables as attribute in html element

in directive.ts
```ts
@Directive({
  selector: '[appBetterHighlight]'
})

export class BetterHighlightDirective implements OnInit {
  @Input() defaultColor = 'transparent';
  @Input() highlightColor = 'blue';
}
```
in html
```html
<p appBetterHighlight defaultColor="gray" [highlightColor]="'orange'">Style hight me!!</p>
```
**NOTE : Thing I did with defaultColor(it's without sqaure bracket) only allow when value is string.**

## 2.2. Structural Directive
**We know**
```html
<div *ngIf="true">
    <p>Something</p>
</div>
```
**behind scene**
```html
<ng-template [ngIf]="true">
    <div>
        <p>Something</p>
    </div>
</ng-template>
```
# 3. SERVICES
- Services is a piece of code or class that acts as a central repository as a central business unit.
- Which can be accessed wherein code.
- service doesn't have decorators like components and directive do.
- normal class in TS will act as service also.

**NOTE: Never instantiated that class on your own, angular provide an efficient way to do that : 
"Angular's dependency injector"**

### 3.1. Angular's dependency injector
1. It's Hierarchical Injector
2. When we provide services to one component, angular will create an instance of that service only for
    - That component(component where you provided service)
    - And child component of that component
3. When you create a new instance of service(by providing service) in the child component, new instance overrides instance from the parent component.

### 3.2. Hierarchical
1. **AppModule**: Same instance throughout the application(can access in any component, directive, service)
2. **AppComponent**: Same instance throughout all component (not in service)
3. **Any Other Component**: Same instance throughout that component and it's child component.

### 3.3. Injecting service into service
Consider two services :  
logging.service.ts
account.service.ts

in account.service.ts
```ts
import { LoggingService } from './logging.service';
import { Injectable } from '@angular/core';

@Injectable()
export class AccountService {
    constructor(private loggingService: LoggingService) {}
    
    addAccount(newName: string, newStatus: string) {
        this.loggingService.logMyStatus(newStatus);
    }
}
```
# 4. ROUTING
in ts
```ts
// Using this we will use router module
import {Routes, RouterModule} from '@angular/router';

// This is a const variable which just holds path in string
const appRoutes = [
    path: '', component: HomeComponent,
    path: 'users', component: UsersComponent,
    path: 'user/:id', component: UserComponent,
];
```
**user/:id** => colon means that id will be dynamic, whatever data you have infront of "user/" will load dynamically.
```ts
// Using this we register routes we declared
// we let angular know there is routes
@NgModule({
    imports : [
        RouterModule.forRoot(appRoutes)
    ]
})
```
**in html**
We can load routes component at specific position as well
```html
<side-section>
<at-bottom>
    <router-outlet>
    </router-outlet>
</at-bottom>
</side-section>
```
### 4.1. Link to route in html
Don't Do this
```html
<a href="/users"></a>
```
Do this
```html
<a routerLink="/users"></a>
```

- **In case of href**: Whenever you click href anchor page get refresh, means angular perform whole life cycle hook.
- **In case of routerLink**: It only loads component or part of the page you want to load

### 4.2. Current Path and Relative Path
---
#### 4.2.1. Current path/ Absolute path
Appends routerLink text infront of "LocalHost" e.g.
```html
<a routerLink="/servers"></a> 
```
when you click : 
localhost:4200/servers

#### 4.2.2. Relative path
Appends routerLink text infront of "Current Path" in address bar e.g.
```html
<a routerLink="servers"></a>
<!-- OR -->
<a routerLink="./servers"></a>
```
when you click 
localhost:4200/servers/servers/servers

**Notice the slash in front of servers remove in Relative path**

## 4.3. Router in TS
in ts
```ts
import { Router, ActivatedRoute } from '@angular/router';

export class ServersComponent implements OnInit {
    constructor(private router: Router, private route: ActivatedRoute) { }

    reloadPage() {
        // this.router.navigate(['servers'], {relativeTo: this.route});
        this.router.navigate(['/servers'], {relativeTo: this.route});
        // Now it will act as routerLink
    }
}
```
- `this.router.navigate(['servers']);` When you do this you expecting that this is relative path.
- But, router don't know the router path (means in which path/location is it). you need to tell it
- that's why we add relativeTo option to `this.router`

### NOTE
Let say you currently on 
https://localhost:4200/servers/4 (servers.component.html page)
and want to go 
https://localhost:4200/servers/4/edit (edit.component.html page)

so in ts function you can do this
```ts
this.router.navigate(['edit'], {relativeTo: this.route})
```
OR
```ts
this.router.navigate(['/servers',4,'edit'])
```
### 4.4. Add Parameters and arguments in Routing Link
#### 4.4.1. In Html template
http://localhost:4200/servers/2/edit?skip=2&top=4#middle-page-scroll
```html
<a [routerLink]="['/servers', 2, 'edit']"
    [queryParams]="{skip: 2, top: 4}"
    fragment="middle-page-scroll">click here</a>
```
#### 4.4.2. In TS
http://localhost:4200/servers/1/edit?top=5&skip=2#go-to-contact
```ts
loadServer(id: number) {
    this.router.navigate(
      ['/servers', id, 'edit'],
      {
        queryParams: {top: 5, skip: 2},
        fragment: 'go-to-contact'
      });
  }
```
### 4.5. We can pass this queryParams to next clicked link
#### 4.5.1. Perserve data
- if we want to perserve or get queryParams of previous routerlink we can user `queryParamsHandling` argument in `route.navigate()` function.
```ts
this.router.navigate(['edit'], {relativeTo: this.route, queryParamsHandling: 'preserve'});
```
#### 4.5.2. Merge data
if we want to merge  queryParams of previous routerlink we can user `queryParamsHandling` argument in `navigate()` function.
```ts
   this.router.navigate(['edit'], {relativeTo: this.route, queryParamsHandling: 'merge'});
```
### 4.6. Observables for route link `ActivatedRoute`
Using Observables you can make asyncronus calls 

In ts
```ts
constructor(private route: ActivatedRoute, private router: Router) { }

// 1. Params
// ----------
this.route.params.subscribe(
    (params: Params) => { this.user.id = params['id']; }
    );

// 2. queryParams
// --------------
this.route.queryparams.subscribe(
    (queryparams: Params) => { allowEdit = queryparams.allowEdit }
    );

// 3. fragment
// -----------
this.route.fragment.subscribe( (fragment: Params) => { 
    // some codes
    }
    );
```
## 4.7 Nested Routing
```ts
const appRoutes: Routes = [
    // default page
    { path : '', component : HomeComponent },

    // some page
    { path : 'users', component : UserComponent },
    
    { path : 'servers', component : ServersComponent, children: [
        {path : ':id', component : ServerComponent},
        {path : ':id/edit', component : EditServerComponent}
        ]
    }
];
```
- So **users** page of UserComponent will load into `<router-outlet></router-outlet>` of `app.component.html` (which is root html page)
    - **app.component.html** => grandparent
    - **servers.component.html** => parent
    - **:id/edit | edit.component.html** => child

- Hence you also need to add `<router-outlet></router-outlet>` at certain div in `servers.component.html` to load `edit.html` page.

### NOTE
**ways to string to int:**
    1. parsInt(stringNumber)
    2. Number(stringNumber)
    3. +stringNumber

## 4.8. Error Handling in routing
#### 4.8.1. Redirect to a certain page
Let say we have page
http://localhost:4200/message

if someone adds link like 'msg' we want to redirect to 'message' page
```ts
  { path: 'message', component: MessageComponent },
  { path: 'msg', redirectTo: '/message' },
```
2. Handle all wrong URL with not-found page
```ts
  { path: 'not-found', component: PageNotFoundComponent },
  { path: '**', redirectTo: '/not-found' },
```
**all wrong URL which are not register in routes are represented by here.**

## 4.9. Hierarchical matters in Routes

- If we have this Hierarchical
```ts
  { path : '', component : HomeComponent },
  { path : 'users', component : UsersComponent },
  { path : 'servers', component : ServersComponent },
  { path: 'not-found', component: PageNotFoundComponent },
  { path: '**', redirectTo: '/not-found' }, // last position
```
and wrong URL appears it going to check line by line and execute **\*\*** (Double star) path at end.

- If we have this Hierarchical
```ts
  { path: '**', redirectTo: '/not-found' }, // first position
  { path : '', component : HomeComponent },
  { path : 'users', component : UsersComponent },
  { path : 'servers', component : ServersComponent },
  { path: 'not-found', component: PageNotFoundComponent }, 
```
Now irrespective of wrong and correct URL angular gonna execute **\*\*** (Double star) path.

**Hierarchical Matters !**

## 4.10. Auth Guard - Routing Guard
1. **canActivate**: Guard component and its all child component
2. **canActivateChild** : Guard only component's child, Not component itself

### 4.10.1. `canActivate`

Create new service let say (auth-guard.service.ts)
```ts
@Injectable()
export class AuthGuard implements CanActivate {
    constructor( private authService: AuthService, private router: Router ) { }
    canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
                    return isUserLoginVariable;
    }
}
```
In Routes in `app-routing.module.ts`
```ts
const appRoutes: Routes =[
    { 
        path: 'servers', canActivate: [AuthGuard], component: ServersComponent, children: [
            { path: ':id', component: ServerComponent },
            { path: ':id/edit', component: EditServerComponent }
        ]
    }
];
```
This will apply auth guard to servers page and it's all child pages.

### 4.10.2. canActivateChild
Just add new interface `canActivateChild` in AuthGUard Service.
It's defination same as `canActivate`
```ts
export class AuthGuard implements CanActivate, CanActivateChild {
    constructor( private authService: AuthService, private router: Router ) { }
    canActivateChild(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot,
    ): Observable<boolean> | Promise<boolean> | boolean {
        return this.canActivate(route, state);
    }
}
```

In Routes in `app-routing.module.ts`
```ts
const appRoutes: Routes =[
    { 
        path: 'servers', canActivateChild: [AuthGuard], component: ServersComponent, children: [
            { path: ':id', component: ServerComponent },
            { path: ':id/edit', component: EditServerComponent }
        ]
    }
];
```
### 4.11. Passing data in route
#### 4.11.1. Static Data 
In Route list 
```ts
{ path: 'not-found', component: ErrorPageComponent, data: { message: 'Page is not found, Murakh!' } },
```
in ErrorPageComponent
```ts
this.route.data.subscribe((data) => {
      this.errorMessage = data.message;
    });
```
#### 4.11.2. Dynamic Data
We need resolver for this 
**STEPS**
1. Create Resolver services
```ts
interface Server {id: number; name: string; status: string;}
@Injectable()
export class ServerResolver implements Resolve<Server> {
    constructor(private serversService: ServersService) { }
    resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Server> | Promise<Server> | Server {
        //implemention
        return this.serversService.getServer(+route.params['id']);
    }
}
```
2. Add it into route you want to
```ts
{ path: ':id', component: ServerComponent, resolve: { server: ServerResolver } },
```
3. Use it where you want
in server.component.ts
```ts
this.route.data.subscribe(
      (data: Data) => {
        this.server = data.server;
      }
    );
```
### 4.12. Understanding Location strategies / Hashtag Routing
`https://localhost:4200/servers`
- This will work fine for the local setup.
- But going to cause a problem when you published/deployed your Single Page Application on the server.

**In Server**
- Your routes/URL always parsed/handled by the server(which hosts our app) first.
`https://www.other_local_server.domain/servers`
- Now the server will look for "/servers" URL in the real server. which is not gonna find.
- We want to angular to take control and parse the routes.

**Solution : Hashtag Routing**
Use hash sign in routing
```ts
@NgModule({
imports: [RouterModule.forRoot(appRoutes, { useHash: true }),]
})
export  class AppRoutingModule {
}
```
`https://www.other_local_server.domain/#/servers`

**Before hash**: will parse by real server
**After hash**: will parse by angular

# 5. OBSERVABLES
- http://reactivex.io/documentation/observable.html
- Observable is an object we import from a third-party package (rxjs).
- It's kind of object of some data structure (Events, Http requests, triggered in code)
- You can handle asynchronous tasks.
- rxjs library provides observables.

![observable-working](./observable-working.PNG)
### 5.1 How it works
1. **Observable** : it follows observable patterns.
2. **Timeline/stream** : can have multiple events emitted by observable/data packages
3. **Observer** : Code you write after subscribe() function.
- You can Handle observables/data packages in three ways/ in three subscribers:
    1. Handle Data
    2. Handle error
    3. Handle COmpletion
- Your code gets executed in these three hooks/boxes.

**Observable doesn't need to complete.**
In Code
E.g.
```ts
this.route.params.subscribe(
// Handle Data subscriber
    (parameter: Params) => {
        this.id = +parameter['id'];
    }
    // Handle error subscriber
    () => {
    }
    // Handle completion of subscriber
    () => {
    }
);
```
-  `this.route.params` : It's Observable of `this.routes`.
-  `this.route.params.subscribe()` : It's Observer.
### 5.2 Create simple Observable
Creat Observable which will generate ascending number in interval of 1 second.
```ts
import { Observable } from  'rxjs/Observable';
import  'rxjs/Rx'; // required for .interval();
export  class HomeComponent implements OnInit {
    constructor() { }
    ngOnInit() {
        // Define the Observable
        const myNumber = Observable.interval(1000);
        // Use Observable
        myNumber.subscribe(
            (num: number) => {
                console.log(num);
            }
        );
    }
}
```
## 5.3 Create Observable from scratch
```ts
import { Observable } from  'rxjs/Observable';
import  'rxjs/Rx';
import { Observer } from  'rxjs/Observer';
export  class HomeComponent implements OnInit {
    constructor() { }
    ngOnInit() {
        // Creating Observable

        // Observable.create() take function as argument and don't confused this with subscribe()
        // it takes and return observer
        const myObservable = Observable.create((observer: Observer<string>) => {
            setTimeout(() => {
                // This next emit normal data package which you recieve in subscribe()
                observer.next('The First Package.');
            }, 2000);

            setTimeout(() => {
                observer.next('The Second Package');
            }, 4000);

            setTimeout(() => {
                // throw an error.
                observer.error('This doesn\'t work.');
            }, 5000);

            setTimeout(() => {
                observer.next('The Third package.');
            });
        });

        // Using Observable
        myObservable.subscribe(
            // data observer
            (data: string) => { console.log(data); },
            // error observer
            (error: string) => { console.log(error); },
            // handle completion observer
            () => { console.log('This complete.'); }
        );
    }
}
```
### NOTE
1. To execute complete observer jusr replace `observer.error()` with `observer.complete()`
```ts
setTimeout(() => {
    observer.complete();
}, 5000);
```
2. Any statement or code after observer.error()` & `observer.complete()` will not execute.
```ts
const myObservable = Observable.create((observer: Observer<string>) => {
    setTimeout(() => {
        observer.next('The First Package.');
    }, 2000);

    setTimeout(() => {
        observer.error('This doesn\'t work.');
        // or
        observer.complete();
    }, 5000);
    // further code will not get executed.
    setTimeout(() => {
        observer.next('The Third package.');
    }, 6000);
});
```
### 5.4 Unsubscribe
- Without Unsubscribe when you navigate away from that component, the subscription of observable not get destroy.
- which keep running continously and will cause memory leak
```ts
import { OnInit, OnDestroy } from  '@angular/core';
import { Observable } from  'rxjs/Observable';
import  'rxjs/Rx';
import { Observer } from  'rxjs/Observer';
import { Subscription } from  'rxjs/Subscription';

export  class HomeComponent implements OnInit, OnDestroy {
    numberObsSubscription: Subscription;
    customObsSubscription: Subscription;
    
    ngOnInit() {
        const myNumber = Observable.interval(1000);
        // Assign subscription here
        this.numberObsSubscription = myNumber.subscribe(
            (num: number) => {
                console.log(num);
            }
        );

        this.customObsSubscription = myObservable.subscribe(
            (data: string) => { console.log(data); },
            (error: string) => { console.log(error); },
            () => { console.log('This complete.'); }
        );
    }

    ngOnDestroy() {
        this.numberObsSubscription.unsubscribe();
        this.customObsSubscription.unsubscribe();
    }
}
```

## 5.5 Operators
- http://reactivex.io/documentation/operators.html
- Operators allow you to transform the data you receive to something else and still stay inside Observable.
- Operators can be used on any observable.
To use Operators import `rxjs\Rx`
```ts
import  'rxjs/Rx';
const myNumber = Observable.interval(1000)
    // map is operator
    .map((data: number) => {
    // each operator always return something
    return data * 100;
});
```
- Operators simple return new observable which you can of course also chain these operator
e.g.
```ts
Observable.interval().map().map().map();
// so on
```
## 5.6 Avoid rxjs-compat package
```sh
npm uninstall rxjs-compat
```
- Now instead of import from 'rxjs\Observable' or 'rxjs\Subject' or 'rxjs\Observer' etc just do
```ts
import {Observable, Observer, Subscription, Subject, interval} from  'rxjs';
```
-  `Observable.interval();` will be just `interval();`
#### For Operators
- Remove `import 'rxjs\Rx';` instead do
```ts
import { map } from  'rxjs/Operators';
// Define the Observable
const myNumber = interval(1000).pipe(map(
    (data: number) => {
    return data * 100;
    }
));
```

# 6. Subject
- http://reactivex.io/documentation/subject.html
- It allows you to Push/emit new data during your code.
- It is similar to the eventEmitter.
- A great option for cross-component communication instead of eventEmitter.
- It acts as Observable and Observer at the same time.
- You can utilize function like observer too like
    - `next()`
    - `error()`
    - `complete()` etc.
- Don't forget to unsubscribe the subject.
#### Define Subject
```ts
import { Subject } from  "rxjs\Subject";
export  class UsersService {
    userActivated = new Subject();
}
```
#### Use as Observable
```ts
// Push data like Observable
this.userActivated.next(this.is);
```
#### Use as Observer
```ts
this.userActivated.subscribe(
    () => {
        // Your code goes here.
    }
);
``
