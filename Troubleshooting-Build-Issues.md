This page lists common issues or FAQ's when building Azure Data Studio.

## Common Issues

### Error `Error: Can only have one anonymous define call per script file` when launching

This is usually caused when adding a new package that supports UMD. VS Code uses an AMD loader for its own files (and ours) but Node.JS normally uses commonjs. For modules that support UMD this may cause some issues since it will appear to them that AMD exists and so depending on the order they check support for the two types they may end up using either commonjs or AMD. If commonjs is first this is the expected method and so nothing needs to be done - but if it's AMD then the VS Code loader will throw the above error.

To fix this we can tell the VS Code loader to force specific packages to be loaded using AMD for those packages in [bootstrap-window.js](https://github.com/microsoft/azuredatastudio/blob/main/src/bootstrap-window.js#L156). Get the name of the package and add it to the `loaderConfig.amdModulesPattern` regex.

## FAQs