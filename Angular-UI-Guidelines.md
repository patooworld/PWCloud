Note that this is not a guide on Angular; this is a guide on the specifics of using Angular inside SQL Operations Studio. 

SQL Operations Studio uses [Angular2](https://angular.io/docs/ts/latest/quickstart.html) to display some UI components. SQL Operations Studio uses a slightly different Angular design philosophy than most Angular applications due to the fact that SQL Operations Studio:
- is a fork of an existing non-Angular app.
- reuses Angular Components from [vscode-mssql](https://github.com/Microsoft/vscode-mssql).
- uses a custom module loader.

The UI for standard Angular applications is handled entirely with Angular. In such applications, a single root Angular Module is [bootstrapped](https://angular.io/docs/ts/latest/guide/appmodule.html), launching with an entry point into a `bootstrap` (root) component. The single root Angular Module persists for the entire duration of the UI's existence. 

SQL Operations Studio's UI is handled differently. SQL Operations Studio has a mixture of both raw JavaScript/HTML and Angular. SQL Operations Studio has various Angular Modules sprinkled throughout its UI, each of which can be constructed or deconstructed at any time. 

SQL Operations Studio's unique UI results in the need to have patterns that differ from what the official documentation suggests. Specifically, SQL Operations Studio differs in the ways described below. 

### Bootstrapping Modules

Bootstrapping is the process of instantiating an instance of an Angular Module. Bootstrapping an Angular Module in SQL Operations Studio requires the following steps.

- Create a root Angular Component. This is what describes the initial UI of the Module. Pass `this._el.nativeElement.tagName` to `BootstrapService.getBootstrapParams` to receive any parameters you pass when calling `bootstrap`. 
```
import { IBootstrapService, BOOTSTRAP_SERVICE_ID } from 'sql/parts/bootstrap/bootstrapService';
declare let AngularCore;

export const MY_SELECTOR: string = 'my-component';

@AngularCore.Component({
  selector: MY_SELECTOR,
  template: '<span>Some HTML</span>'
})
export class MyComponent {

  private myParams: any;

  constructor(@AngularCore.Inject(BOOTSTRAP_SERVICE_ID) bootstrapService: IBootstrapService) {
    this.myParams = bootstrapService.getBootstrapParams(this._el.nativeElement.tagName);
  }
}
```

- Create a Module using the Component you created an `entryComponent`. Override `ngDoBootstrap` as follows with the selector and type of your root component. 
```
import { IBootstrapService, BOOTSTRAP_SERVICE_ID } from 'sql/parts/bootstrap/bootstrapService';
import { ApplicationRef, ComponentFactoryResolver } from '@angular/core';
import { MyComponent, MY_SELECTOR } from 'sql/example/myComponent';
declare let AngularCore;

@AngularCore.NgModule({
  imports: [ AngularCommon.CommonModule, AngularPlatformBrowser.BrowserModule ],
  declarations: [ MyComponent ], 
  entryComponents: [ MyComponent ]
})
export class MyModule {
  constructor(
    @AngularCore.Inject(AngularCore.forwardRef(() => AngularCore.ComponentFactoryResolver)) private _resolver: ComponentFactoryResolver,
    @AngularCore.Inject(BOOTSTRAP_SERVICE_ID) private _bootstrapService: IBootstrapService
  ) {
  }

  ngDoBootstrap(appRef: ApplicationRef) {
    const factory = this._resolver.resolveComponentFactory(MyComponent);
    const uniqueSelector: string = this._bootstrapService.getUniqueSelector(MY_SELECTOR);
    factory.selector = uniqueSelector;
    appRef.bootstrap(factory);
  }
}
```
- Call `BootstrapService.bootstrap` and pass in any parameters you want your root Component to receive. (e.g. `myParams` in the Component example above will receive `{ myNumber: 1234, myBool: true }`.  The module will be bootstrapped and appended to the HTML element you pass. 
```
import { IBootstrapService } from 'sql/parts/bootstrap/bootstrapService';
import { MyModule, MY_SELECTOR } from 'sql/example/myModule';

... 

let params: any = { myNumber: 1234, myBool: true };
let htmlElement: HTMLElement = this.getContainer().getHTMLElement();
bootstrapService.bootstrap(
  MyModule,
  htmlElement,
  MY_SELECTOR,
  params);

```

### Detecting UI Changes

Programmatic changes to the UI can go undetected by Angular. For example, if I dynamically change a class variable that is bound to the Angular UI, Angular may not notice. In this case, you have to manually tell Angular to detect changes.  

Let's say you have a function `makeUiChanges()` that dynamically makes changes to the UI. To get Angular to notice these changes, you can either:
- Use `ChangeDetectorRef` to manually check for changes:
```
import { ChangeDetectorRef } from '@angular/core';
declare let AngularCore;

...

constructor(@AngularCore.Inject(AngularCore.forwardRef(() => AngularCore.ChangeDetectorRef)) cd: ChangeDetectorRef) {}

...

makeUiChanges();
this.cd.detectChanges();
```

- Run all UI-changing code inside of `NgZone.run` to automatically check for changes:
```
import { NgZone } from '@angular/core';
declare let AngularCore;

...

constructor(@AngularCore.Inject(AngularCore.forwardRef(() => AngularCore.NgZone )) zone: NgZone ) {}

...

this.zone.run(() => {
  makeUiChanges();
});

```
