This page lists common issues or FAQ's when building Azure Data Studio.

## Common Issues

### Error `Error: Can only have one anonymous define call per script file` when launching

This is usually caused when adding a new package that supports UMD. VS Code uses an AMD loader for its own files (and ours) but Node.JS normally uses commonjs. For modules that support UMD this may cause some issues since it will appear to them that AMD exists and so depending on the order they check support for the two types they may end up using either commonjs or AMD. If commonjs is first this is the expected method and so nothing needs to be done - but if it's AMD then the VS Code loader will throw the above error.

To fix this we can tell the VS Code loader to force specific packages to be loaded using AMD for those packages in [bootstrap-window.js](https://github.com/microsoft/azuredatastudio/blob/main/src/bootstrap-window.js#L156). Get the name of the package and add it to the `loaderConfig.amdModulesPattern` regex and then add an entry to the path of the module to the `loaderConfig.paths` property above.

NOTE: If the module is currently being loaded directly by the Node.JS require (`__$__nodeRequire`) such as the ones in [sql/setup.js](https://github.com/microsoft/azuredatastudio/blob/main/src/sql/setup.js) then adding them to the above lists won't work as the Node require path bypasses the logic that looks at those. You have two options at that point: 

1. Move the module to be loaded using `import` instead
2. Temporarily set `define.amd` to false so that the module thinks that AMD isn't supported and so loads using commonjs. See https://github.com/microsoft/azuredatastudio/pull/18093 for an example and some more details

## FAQs