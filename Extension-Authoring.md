If you're interested in extending Azure Data Studio, you can do that by creating your own extension and publish it to the Azure Data Studio extension gallery.

**Writing an Extension**

***Prerequisites***

To develop an extension you need Node.js installed and available in your $PATH. Node.js includes npm, the Node.js Package Manager, which will be used to install the extension generator.

To start your new extension, you can use the Azure Data Studio Extension generator. The Yeoman [extension generator](https://www.npmjs.com/package/generator-azuredatastudio) makes it very easy to create simple extension projects. To Launch the generator, type the following in a command prompt:

`npm install -g yo generator-azuredatastudio`

`yo azuredatastudio`


**Extensibility References**

Please see [Extensibility References](https://github.com/Microsoft/azuredatastudio/wiki/Getting-started-with-Extensibility) to learn about Azure Data Studio Extensibility API. You can also see examples of how to use the API in existing [samples](https://github.com/Microsoft/azuredatastudio/tree/master/samples).


**Debugging Extension**

You can debug your new extension using Visual Studio Code extension [SQL Operations Studio Debug](https://github.com/kevcunnane/sqlops-debug).

Steps
- Open your extension with [Visual Studio Code](https://code.visualstudio.com/)
- Install Azure Data Studio Debug extension
- Press F5 or click the Debug icon and click Start
- A new instance of Azure Data Studio will start in a special mode (Extension Development Host) and this new instance is now aware of your extension.


**Creating Extension Package**

After writing your extension, you need to create a VSIX package to be able to install it in Azure Data Studio. You can use [vsce](https://github.com/Microsoft/vscode-vsce) to create VSIX package.

`npm install -g vsce`

`npm install`

`vsce package`


## **Publishing the Extension**

To publish your new extension to Azure Data Studio
1. Add your extension to the extension gallery file or edit it if updating your extension. update these 2 files accordingly.
If the new extension release works with the latest stable version of ADS update this file: https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery.json
If the new extension release works with the insiders build of ADS update this file:
https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery-insider.json

1. Upload the VSIX package

Third party extensions:
We don't have support to host third party extensions for now. Instead of downloading the extension, ADS has the option to browses to the download page. To be able to set a download page for your extension please set the value of asset "Microsoft.SQLOps.DownloadPage".

Microsoft extensions:
contact alanren/chgagnon for help.

There are two separate processes to follow based on when the extension is meant to be updated. Typically stable release updates should follow the second
### 1. Standalone extension release (not part of an ADS Release)

1. Create a PR against release/extensions branch
  * Make sure to add the extension to both the insiders and stable gallery files
1. Send a review request to @alanrenmsft and @Charles-Gagnon

### 2. Extension release as part of ADS release

*Note* It's suggested to publish the new version of the extension to the Insiders gallery as soon as you have it for early testing/validation (see above steps)
1. There should be a PR already opened for the release to update extensions - if not contact the Release DRI to have that done
1. Push changes to the PR branch with the gallery updates needed

Your extension will be reviewed and will be added to the extension gallery.

**Publishing Extension Updates**
The process to publish updates is similar to publishing the extension. Please make sure the version is updated in your package.json after a release is done so that it's ready for the next release cycle. 
