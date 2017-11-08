**Q:** How do I get my repository setup?   
**A:** See [How to Contribute](How-to-Contribute).

**Q:** How do I build?   
**A:** Run `npm run watch` from the root directory. This will run a compiler that runs in the background and listens for changes you make to source files. When it notices changes, it will compile only the files that need to be recompiled. This is considerably faster than running a `npm run compile` every time you make changes. Note that `npm run watch` will never terminate (unless it crashes). If it outputs `Finished compilation with 0 errors after`, then it is done compiling for the moment. 

**Q:** How do I install the npm modules?   
**A:** Run `.\scripts\npm.bat install` on Windows, `./scripts/npm.sh` install on OSX, or `./scripts/npm.sh install --arch=x64` on Linux. If this command is failing, try cleaning your repository (see _How do I clean my repository?_) then running `npm cache clean`, then trying again. 

**Q:** How do I clean my repository?  
**A:** `git clean -dfx` 

**Q:** How do I run the tests?   
**A:** Run `.\scripts\test.bat` on Windows, `./scripts/test.sh` on OSX, or `./scripts/test.sh` on Linux.

**Q:** What do I do if the tests hang forever?   
**A:** Delete the `.build` directory, run `gulp electron`, then run the tests again. If this doesn't work, do a full clean of your repository (`git clean -dfx`), an npm clean (`npm cache clean`), and a reinstall of the npm modules (see _How do I install the npm modules?_). 

**Q:** What are the `@` things I see throughout the source code?   
**A:** These are decorators that enable custom dependency injection. They are a pattern introduced in the vscode source. Using them will allow the InstantiationService (vscode's replacement for calling `new`) to inject singleton dependencies. If you need a singleton service injection, put a `@` parameter in your constructor: 
```
constructor(@IContextMenuService private _contextMenuService: IContextMenuService) {}
```
Note that all `@` parameters must come before non-`@` parameters. For example, this is not valid:
```
constructor(private myNumber: number, @IContextMenuService private _contextMenuService: IContextMenuService) {}
```

But this is valid:
```
constructor(@IContextMenuService private _contextMenuService: IContextMenuService, private myNumber: number) {}
```

**Q:** How do create an instance using the InstantiationService?   
**A:** Let's say you want to create an instance of `MyClass` that looks like:
```
export class MyClass {
  constructor(
    @IContextMenuService private _contextMenuService: IContextMenuService,
    @IThemeService themeService: IThemeService,
    someNumer: number, 
    someString: string) {
  }
}
```
- Import InstantiationService and MyClass: 
```
import { IInstantiationService } from 'vs/platform/instantiation/common/instantiation';
import { MyClass } from 'sql/example/myClass';
```
- Request for the InstantiationService to be injected in the constructor:
```
constructor(@IInstantiationService private _instantiationService: IInstantiationService) {}
```

- Call `createInstance`, passing in all parameters in the MyClass constructor that aren't pretended with `@` in the order they appear:  
```
let myClass = this._instantiationService.createInstance(MyClass, 1234, 'a string');
```

If you give the wrong type/quantity of parameters or if there are non-`@` parameters before the `@` parameters, you will get a cryptic compile error:
```
Argument of type 'typeof MyClass' is not assignable to parameter of type 'AsyncDescriptor3<this, number, string {}>'.
  Property 'moduleName' is missing in type 'typeof MyClass'.
```

**Q:** How can I inspect the HTML or CSS?    
**A:** Install the "Debugger for Chrome" extension for vscode. Run SQL Operations Studio. Detach the vscode debugger if you have it attached. Then go to Help -> Toggle Developer Tools. This will launch the Chrome Debugger. The "Select an element in the page to inspect it Ctrl + Shift + C" icon is particularly helpful. 

**Q:** What is the difference between editors and inputs?   
**A:** Editors contain the UI logic and are intended to be stateless. Inputs contain the state for editors and do not alter the UI. There can be many inputs for a single editor. 

If I have 5 .sql tabs and 2 .txt tabs, then I have 5 instances of QueryInput, 2 instances of FileEditorInput, 1 QueryEditor, and 1 TextResourceEditor. If I switch tabs between 2 different .sql tabs, the same QueryEditor will be used but will switch between 2 different QueryInput instances, each of which corresponds to one of the tabs. 

In the case where there are split editors (2 editors simultaneously side-by-side, one on the left and one on the right) then there will be 1 editor for the left side and 1 editor for the right side. 

**Q:** How do I debug Sqltoolservice code from sqlopsstudio IDE?   
**A:** As a prerequisite we assume you have the latest source code of both sqlopsstudio and Sqltoolservice code cloned and compiled or build. If not, you could follow the respective project wiki pages.

**Steps**:  
1. Open file <sqlopsstudio repo root path>\extensions\mssql\client\src\config.json. Under service property, check the version value of sqltoolservice extension. For eg., "version": "1.2.0-alpha.1".  
2. Open build folder path for sqltoolsservice: <sqltoolsservice root path>\src\Microsoft.SqlTools.ServiceLayer\bin\Debug\netcoreapp2.0\publish.  
3. Copy the sqltoolsservice binaries from above publish folder to sqlopsstudio sqltoolsservice extension path ([sqlopsstudio repo root path]\extensions\mssql\sqltoolsservice\Windows\[Version]\). The [Version] should be the same as version value in step 1.  
4. Run SQL Operations Studio.  
5. Open Sqltoolservice project in VS code IDE.   
6. Select Debug in the Activity Bar. Select 'Attach(Remote Debug)' in the drop-down next to debug 'Play' icon.  
7. Click on the Debug/Play button.  
8. A drop-down menu appears with the list of processes. Select 'MicrosoftSqlToolsServiceLayer.exe'.  
9. The debugger should attach successfully.