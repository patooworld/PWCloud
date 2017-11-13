# Importing npm modules
## Adding to the package.json

This repository uses _npm-shrinkwrap_, which will overwrite anything in the package.json. Therefore, just running `npm install --save {node module}` will not be enough to make those module download during `npm install`. The process to add a non-dev only dependency to SQL Operations Studio is below. 
  - Note that for dev only dependencies the below steps can be ignored as _npm-shrinkwrap_ doesn't matter. Using `npm install --save-dev {node module}` will work just fine
  - While the below steps look complicated, they get around the inherent issues with shrinkwrap and are based on current known best practices. If you have an update / suggestion on improving please let us know!

### Add a production npm dependency to the main application
1. Either run `npm install --save {node module}` or add the node module to the package.json directly.

2. Run `scripts/npm.{bat/sh} install`

3. Run `npm prune --production` until it stops removing node modules (sometimes it will require a few runs, but they are relatively short); this step is to make sure we are only adding production dependencies to the npm-shrinkwrap file.

5. For good measure, run `npm prune` to ensure there is no left over modules.

6. Run `npm shrinkwrap`

In theory, this will result in the only modification done to the npm-shrinkwrap.json being the addition of your new module. If this is not the case this means one or more of a few different things went wrong.

- The npm-shrinkwrap.json was already out of sync with the package.json, which should never be the case if everyone if following this process when adding a production node module.

- You did not run `npm prune --production` enough times and you still have dev dependencies laying in your node modules. If this is the case, run `npm prune` and `npm prune --production` until no more modules are removed, then rerun `npm shrinkwrap`.

### NPM shrinkwrap errors

If you get errors during `npm shrinkwrap` that refer to extraneous packages, this means you need to run `npm prune`.

If you get errors about packages being expected but none exists, this means one of two things.

- The package does not exist in your node modules folder and you need to run `scripts/npm.{bat/sh} install`.

- The package does exist in your node modules folder, but one of the other packages which depends on it is expecting a different version. This is most commonly the problem when multiple packages depend on a single package, but on different versions of that package. If this is the case, there is a much bigger problem that needs to be resolved. You can either directly modify the dependency in your node modules folder to be the version you have locally to make `npm shrinkwrap` happy (_warning_: this is hacky and almost certainly not the proper solution), or you need to figure out how to make the two packages depend on the same version.

### Want to be triple sure it worked correctly?

Reinstall dev dependencies by running `scripts/npm.{bat/sh} install`, then run `gulp vscode-win32`. This will go through building, optimizing, and bundling. If this is successful it is a good bet you added the node module correctly.

## Getting node modules to resolve at run time
To use your node module inside the application code you will need to register with the custom loader so it is available at runtime. Once this is done you can use `import` as normal inside the application code to reference and use the module.

### How to register a new node module with the custom loader
In the file `src/vs/workbench/electron-browser/bootstrap/index.js` there is a main function which is were the customer loader is initialized. In this function there is a line of code

```
require.config({
  ...
  nodeModules: [
    ...
  ]
})
```

Any new node modules need to be added here so that the loader knows to treat these imports as node modules at runtime. The string to add here is whatever string you would import from, ex. `@angular/core` or `rxjs/Observable` (there is a string match in the loader so it must match exactly).

## Getting node modules to resolve during tests

For node modules to be resolved during tests, there is a similar line of code located at `test/all.js`.

```
var loaderConfig = {
  ...
  nodeModules: [
    ...
  ]
}
```

Like above you must add the node modules here exactly as you would when importing it.


## Getting node modules to resolve during the optimization step

For node modules to be resolved during the vscode optimization step, the process is slightly different. In the file `build/gulpfile.vscode.js` that has a const declared near the top of the file called `const nodeModules` where node modules can be declared, however, by default the optimization task populates node modules from the npm-shrinkwrap file. Therefore, if the node module appears in the npm-shrinkwrap as you would import it, you do not need to modify this const (ex. `@angular/core`). However, if it does not appear in the shrinkwrap as you would import it (ex. `rxjs/Observer`), you must add it to this line.

# Special NPM modules

## rxjs
When using rxjs object, most references will say to import from `rxjs/Rx`. However, importing from this file imports everything from rxjs, which adds a lot of needless objects. Therefore, instead, you can import from the specific module you need. Example: if you need `Observable` instead of

```
import { Observable } from 'rxjs/Rx';
```

You should use

```
import { Observable } from 'rxjs/Observable;
```

This will only load the Observable file, rather than the entire library.

# Solving circular dependency issues

The vscode module loader cannot handle dependencies that are circular at run time. Let's say I have 2 classes - `A` and `B`. If `A` imports and directly uses `B` at run time and `B` imports and directly uses `A` at run time, the vscode loader will throw and crash the application before startup finishes. Run time (as opposed to compile time) usage can be anything from using `... instanceof A` to `let myA = new A();` Compile time usage can be any typing-related syntax, e.g. `let myA: A;`.

The solution to these circular dependency issues is generally to separate one of the classes into an interface and and implementation, then place the interface and implementation into separate files. Whenever possible, use and import the interface rather than the implementation.  