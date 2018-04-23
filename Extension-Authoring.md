If you're interested in extending SQL Operation Studio, you can do that by creating your own extension and publish it to SQL Ops extension gallery.

**Writing an Extension**

***Prerequisites***

To develop an extension you need Node.js installed and available in your $PATH. Node.js includes npm, the Node.js Package Manager, which will be used to install the extension generator.

To start your new extension, you can use the SQL Operations Studio Extension generator. The Yeoman [extension generator](https://www.npmjs.com/package/generator-sqlops) makes it very easy to create simple extension projects. To Launch the generator, type the following in a command prompt:

`npm install -g yo`

`yo sqlops`


**Extensibility References**

Please see [Extensibility References](https://github.com/Microsoft/sqlopsstudio/wiki/Getting-started-with-Extensibility) to learn about SQL Operations Studio Extensibility API. You can also see examples of how to use the API in existing [samples](https://github.com/Microsoft/sqlopsstudio/tree/master/samples).


**Debugging Extension**

You can debug your new extension using Visual Studio Code extension [SQL Operations Studio Debug](https://github.com/kevcunnane/sqlops-debug).

Steps
- Open your extension with [Visual Studio Code](https://code.visualstudio.com/)
- Install SQL Operations Studio Debug extension
- Press F5 or click the Debug icon and click Start
- A new instance of SQL Operations Studio will start in a special mode (Extension Development Host) and this new instance is now aware of your extension.


**Creating Extension Package**

After writing your extension, you need to create a VSIX package to be able to install it in SQP Operations Studio. You can use [vsce](https://github.com/Microsoft/vscode-vsce) to create VSIX package.

`npm install -g vsce`

`vsce package`


**Publishing the Extension**

To publish your new extension to SQL Ops Studio
1. Create a new GitHub issue (https://github.com/Microsoft/sqlopsstudio/issues)
2. Specify the location of extension's package.json
3. Specify the web page to download the vsix from
4. Assign the new issue to llali 

Your extension will be reviewed and will be added to the extension gallery.

**Publishing Extension Updates**
The process to publish updates is similar to publishing the extension. Please make sure the version is updated in package.json
