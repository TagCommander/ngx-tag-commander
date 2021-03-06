# ngx-tag-commander

This service lets you integrate Tag Commander in your AngularX (2+) applications easily.
- [Official website](https://www.commandersact.com/fr/produits/tagcommander/)

## Features

 - Automatic page tracking
 - Set & Get Variables
 - Reloading Containers
 - Event catching
 - Multiple containers

## Installation and Quick Start
The quick start is designed to give you a simple, working example for the most common usage scenario. There are numerous other ways to configure and use this library as explained in the documentation.

### 1- Before installing the plugin

The plugin doesn't replace the standard setup of a container because you may need to use the containers outside of the plugin.

Initialize your datalayer so that it's ready for the container and plugin, without losing any data. Do it as soon as possible on your website like in a `<script>` block in the head of your webapp.

```
tc_vars = []
```
### 2- Installation:


You can install the module from a package manager of your choice directly from the command line

```
# NPM
npm i ngx-tag-commander
```


### 3- In your application app.module.ts, declare dependency injection:


```typescript
...
import { WindowRef } from 'ngx-tag-commander';
...
import { NgxTagCommanderModule } from 'ngx-tag-commander';

@NgModule({
    ...
    imports: [
        ...
        NgxTagCommanderModule
        ...
    ],
    providers: [WindowRef],
    ..
})

```

### 4- add your Tag commander containers and start tracking:

```typescript
import { TagCommanderService } from 'ngx-tag-commander';
...
export class AppModule {
  constructor(tcService: TagCommanderService) {
    ...
    Promise.all([
      tcService.addContainer('container_body', '/assets/tag-commander-body.js', 'body'),// Replace URL by the one of your container
      tcService.addContainer('container_head', '/assets/tag-commander-head.js', 'head')
    ]).then(() => {
      //Insert the rest of your code here
    })
    ...
  }
}
```

You are now ready to use the ngx-tag-commander plugin.

## Declaring TagCommander in your Controller
```typescript
import { TagCommanderService } from 'ngx-tag-commander';
...
export class MyComponent {
constructor(private tcService: TagCommanderService) { }
}
```

## Declaring the route tracking
first configure the module to track routes in app.module

```typescript
export class AppModule {
  constructor(tcService: TagCommanderService) {
    ...
    tcService.trackRoutes(true);
    ...
  }
}
```
then in your routes:

```typescript
const appRoutes: Routes = [
  {
    path: '',
    redirectTo: '/home',
    pathMatch: 'full',
    data: {
      tcInclude: [{
        'idc':  12,
        'ids': 4056,
        options: {
          // Data Layer options if needed
        }
      },
      {
        'idc':  11,
        'ids': 4055,
      }]
    }
  },
 ];
```
This will reload the specified containers, with the specified options

## Set Vars
The `setTcVars` call allows you to set your `tc_vars`.

```typescript
constructor(private tcService: TagCommanderService) {
    tcService.setTcVars({
    env_template : "shop",
    env_work : "dev",
    env_language : "en",
    user_id : "124",
    user_logged : "true",
    user_age: "32",
    user_newcustomer : "false",
    });
  // you can also override some variable
    if (isNewUser) {
      tcService.setTcVars({
      user_newcustomer : "true",
      });
    }
    // or set/update them individually
    tcService.setTcVar('env_template', 'super_shop');
}
```
### As a directive
You can use the directive tcSetVars directly on any html node

```html
<html-element class="sm-button green-500" [tcSetVars]="{'env_language': 'fr'}"></html-element>

<!-- other exemple -->
<!-- defaultLanguage being an attribut of your component -->
<div class="sm-button green-500" [tcSetVars]="{'env_template': defaultEnv}"></div>
```
## Retrieve or Delete Variables

```typescript
import { WindowRef } from 'ngx-tag-commander';

constructor(private tcService: TagCommanderService {
	...
	// retrieve container variables
    tcService.getTcVar('your_var_key'));
    // remove variable
    tcService.removeTcVar('your_var_key')
  }
```


## Capture Events
```typescript
constructor(private tcService: TagCommanderService) {
    // {string} eventLabel the name of your event
    let eventLabel=  'NameEvent';
    // {HTMLElement} element the HTMLelement on which the event is attached
    let element = 'button';
    // {object} data the data you want to transmit
    let data = {"env_language": 'theEventVar'};
  
    tcService.captureEvent(eventLabel,element, data);
   }
```
### As a directive
```html
<button [tcEvent]="'test'" [tcEventLabel]="'test'" [tcEventObj]="cartItems"> Add Items in ShopCart </button>

```

## How to reload your container
In your app.module.ts on one of your routes please write tcInclude inside the data part. You also need to state the event Page in the options.

```typescript
var idc = '1234';
var ids = '1234';
var options = {
    events: {page: [{},{}]},
    // ...
    // other options
    // ...
};
this.tcService.reloadContainer(ids, idc, options);
// or you can reload all the containers
this.tcService.reloadAllContainers(options);
```
## Automatic reload of your containers by tracking Routes
### The configuration

you need to set tcService.trackRoutes(true); to true in your app configuration

```typescript
const appRoutes: Routes = [
  {
    path: '',
    redirectTo: '/home',
    pathMatch: 'full',
    data: {
      tcInclude: [{
        idc:  12,
        ids: 4056,
        options: {
          // Data Layer options
        }
      }]
    }
  },
  {
    path: 'home',
    component: IndexPageComponent,
    data: {
      tcInclude: [{
        idc:  12,
        ids: 4056,
        options: {
			// Data Layer options
        }
      }]
    }
  }
];
```
### Exclusions

You can state an exclusion array to your options object like below :

```typescript
tcInclude: [{
	idc:  12,
	ids: 4056,
	options: {
	    exclusions: [
        "datastorage",
        "deduplication",
        "internalvars",
        "privacy"
    	]
	}
}]

```
Please see Tag Commander documentation for other options


## Sample app
To help you with your implementaiton we provided a sample application. to run it
```
ng serve
or
npm start
```
then go to [http://localhost:4200/](http://localhost:4200/)


## Development

With Node 10

After forking you will need to run the following from a command line to get your environment setup:

1. ```yarn install```

After install you have the following commands available to you from a command line:

Build the library by :

 ```ng build NGX-TAG-COMMANDER ```

## Documentation

- ```TagCommanderService.addContainer( id: string, uri : string, node : string ): Promise```

	- id : id the id the script node will have
	- uri : uri the source of the script
	- node : the node on witch the script will be placed, it can either be head or body

  Return a promise which is resolved when the container has been loaded.

- ```TagCommanderService.removeContainer( id : string )```

	- id : id the id the script node will have


- ```TagCommanderService.setDebug( debug : bool )```

	- debug : will display the debug messages if true


- ```TagCommanderService.trackRoutes( b : bool )```

 - b : will read routes if set to true



- ```TagCommanderService.setTcVar( tcKey : string, tcVar : any )```

	set or update the value of the var

	- tcKey : key in the data layer
	- tcVar : content


- ```TagCommanderService.setTcVars( vars : any )```

	set your variables for the different providers, when called the first time it instantiate the external variable

- ```TagCommanderService.getTcVar( tcKey : string )```

	get the value of the container variable
	
	- tcKey : key 


- ```TagCommanderService.removeTcVar( varKey : string )```

	removes the var by specifying the key
	
	- varKey : key of the variable


- ```TagCommanderService.reloadAllContainers( options : object )```

	Reload all containers

	- options to give to the ```tC.container.reload(options)``` function

- ```TagCommanderService.reloadContainer( ids : number, idc : number, options : object )```

	Reload the specified container
	- ids : Site Id
	- idc : Container Id
	- options : options for the function ```tC[containerId].reload(options)```

- ```TagCommanderService .captureEvent( eventLabel : string , element : HTMLElement, data : object )```

	Set a TagCommander Event
	- eventLabel : name of the event
	- element : Dom Element where the event is attached
	- data : data you want to send


## License

As AngularJS itself, this module is released under the permissive [MIT License](http://revolunet.mit-license.org). Your contributions are always welcome.


