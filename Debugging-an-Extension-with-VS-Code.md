# Debugging an Extension with VS Code

If you’re looking to build Azure Data Studio extensions you may have noticed there’s no debugging experience without jumping through some hoops. Well now worry no more! In Visual Studio Code, if you install the `sqlops-debug` extension you can launch Azure Data Studio with your extension installed and auto-attach to the extension host like normal.

Install the `Azure Data Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
 
**Why can't we just use the VS Code extension debugger?**
The debugger for VS Code extensions launches the extension in a new VS Code instance. In order for the extension to be launched in Azure Data Studio a new debugger type needed to be created that would correctly launch Azure Data Studio in Extension Host mode instead. 
 
**Github Project:** https://github.com/microsoft/azuredatastudio-debug
 
**Install Instructions**
*To debug an extension in the Azure Data Studio installed on your machine:*
-	Open Azure Data Studio and add azuredatastudio to your path using the `Shell Command: Install 'azuredatastudio' command in PATH` command
-	Install the `Azure Data Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
-	Open your extension in VS Code and add the following launch.json file 
-	Hit F5 to debug! Note that attach & test work too (just change the “type” to sqlopsExtensionHost and it’ll work)
 
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "SqlOps Extension",
            "type": "sqlopsExtensionHost",
            "request": "launch",
            "runtimeExecutable": "azuredatastudio",
            "args": [
                "--extensionDevelopmentPath=${workspaceRoot}"
            ],
            "stopOnEntry": false,
            "sourceMaps": true,
            "outFiles": [
                "${workspaceRoot}/out/**/*.js"
            ],
            "preLaunchTask": "",
            "timeout": 25000
        }
    ]
}
```

*To debug against an Azure Data Studio Enlistment*
-	Create an environment variable ADS_DEV (or similar) pointing to the root of your enlistment.
-	Install the `Azure Data Studio Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
-	Open your extension in VS Code and add the following launch.json file
-	Hit F5 to debug! Note that attach & test work too (just change the “type” to sqlopsExtensionHost and it’ll work)
 
```
// A launch configuration that compiles the extension and then opens it inside a new window
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Azure Data Studio Extension",
            "type": "sqlopsExtensionHost",
            "request": "launch",
            "windows": {
                "runtimeExecutable": "${env:ADS_DEV}/scripts/sql.bat",
            },
            "osx": {
                "runtimeExecutable": "$ADS_DEV/scripts/sql.sh"
            },
            "linux": {
                "runtimeExecutable": "$ADS_DEV/scripts/sql.sh"
            },
            "args": ["--extensionDevelopmentPath=${workspaceRoot}" ],
            "stopOnEntry": false,
            "sourceMaps": true,
            "outFiles": [ "${workspaceRoot}/out/**/*.js" ],
            "preLaunchTask": "",
            "timeout": 25000
        
    ]
}
```

