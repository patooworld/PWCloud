Typing files enable the easy usage of JavaScript files in TypeScript projects. For SQL Operations Studio, the typings files are located at `/src/typings`. If you add a new npm dependency or update an existing one, you may need to update the typings files in order to enable proper compilation and intellisense. 

Steps:
- Add the dependency to the `/package.json` and `/shrinkwrap.json` files. 
- Install the dependency:
   - Windows: `scripts\npm.bat install` or `npm install <new dependency>`
   - Mac/Linux: `scripts/npm.sh install` or `npm install <new dependency>`
- Navigate to the `/src/` directory
- Run `typings install --save <location to new dependency root .d.ts file>`. Add the `--global` flag if typings says that the package must be installed as a global 

For example, if I want to add `@angular/core` at version `~2.1.2` and I am using Windows, I can:
- Add `"@angular/core": "~2.1.2"` to `/project.json` under `dependencies`
- Add the following to `/shrinkwrap.json` under `dependencies` (ideally done via `npm shrinkwrap`): 

```
    "@angular/core": {
      "version": "2.1.2",
      "from": "@angular/core@>=2.1.0 <2.2.0",
      "resolved": "https://registry.npmjs.org/@angular/core/-/core-2.1.2.tgz"
    }
```
- `npm install @angular/core`
- `cd src`
- `typings install --save npm:@angular/core/index.d.ts`