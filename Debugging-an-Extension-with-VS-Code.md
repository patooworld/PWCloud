# Debugging an Extension with VS Code

If you’re looking to build Azure Data Studio extensions you may have noticed there’s no debugging experience without jumping through some hoops. Well now worry no more! In Visual Studio Code, if you install the `sqlops-debug` extension you can launch Azure Data Studio with your extension installed and auto-attach to the extension host like normal.

Install the `SQL Operations Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
 
**Why didn’t this “just work” before now?**
We removed the DebugService in SQL Ops Studio. This does some magic when VS Code is launched in the extension-testing mode. The “fix” is to add a sqlopsExtensionHost debugger type to the node debugger that does the attaching for you.
 
**Github Project:** https://github.com/kevcunnane/sqlops-debug 
 
**Install Instructions**
*To debug an extension in the SQL Ops Studio installed on your machine:*
-	Open Azure Data Studio and add azuredatastudio to your path using the `Shell Command: Install 'azuredatastudio' command in PATH` command
-	Install the attached `SQL Operations Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
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
-	Create an environment variable SQLOPS_DEV (or similar) pointing to the root of your enlistment.
-	Install the attached `SQL Operations Studio Debug` extension from https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug
-	Open your extension in VS Code and add the following launch.json file
-	Hit F5 to debug! Note that attach & test work too (just change the “type” to sqlopsExtensionHost and it’ll work)
 
```
// A launch configuration that compiles the extension and then opens it inside a new window
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "SqlOps Extension",
            "type": "sqlopsExtensionHost",
            "request": "launch",
            "runtimeExecutable": "$SQLOPS_DEV/scripts/sql.sh",
            "args": ["--extensionDevelopmentPath=${workspaceRoot}" ],
            "stopOnEntry": false,
            "sourceMaps": true,
            "outFiles": [ "${workspaceRoot}/out/**/*.js" ],
            "preLaunchTask": "",
            "timeout": 25000
        
    ]
}
```

