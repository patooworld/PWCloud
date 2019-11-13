we have deprecated the sqlops namespace and introduced a new namespace azdata. please follow the steps below to update your extension if you are still using sqlops.

1. Open your package.json, under devDependencies, find the sqlops entry:

```
	"sqlops": "github:anthonydresser/sqlops-extension-sqlops"
```
and replace it with
```
        "azdata": "^1.0.0"
```
2. Replace all the sqlops call with azdata. The list below shows the list of the methods that were moved or renamed, other than that you can simply replace sqlops with azdata.

* sqlops.window.createDialog -> azdata.window.createWebViewDialog
* sqlops.window.modelviewdialog.createTab -> azdata.window.createTab
* sqlops.window.modelviewdialog.createButton -> azdata.window.createButton
* sqlops.window.modelviewdialog.openDialog -> azdata.window.openDialog
* sqlops.window.modelviewdialog.closeDialog -> azdata.window.closeDialog
* sqlops.window.modelviewdialog.createWizardPage -> azdata.window.createWizardPage
* sqlops.window.modelviewdialog.createWizard -> azdata.window.createWizard

Please open an issue if you have any questions.

