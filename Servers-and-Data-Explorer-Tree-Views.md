In addition to the built-in [VS Code tree views](https://vscode-westeu.azurewebsites.net/api/extension-guides/tree-view) which Azure Data Studio inherits, there are two main other types of trees that can be contributed to. 

# General Information

## Context Menus

Context Menus are the menus of available actions that appear when a user right clicks on an item in a tree. These are often dynamically generated based on the "context" of the item in question, for example if you right click on a Database it will have different actions available than a table. 

Context menu items are added in one of two ways, depending on whether the contribution comes from an extension or from core.

### Core

### Extensions

See [context variables](https://github.com/microsoft/azuredatastudio/wiki/Context-Variables) for information on available variables to control visibility. See [VS Code contributes.menus](https://code.visualstudio.com/api/references/contribution-points#contributes.menus) for general information on menu item contributions from extensions. 

# Servers (Object Explorer)

The Servers (also commonly referred to as Object Explorer, similar to SSMS) tree view is the main tree view of the `Connections` view container. This is where server connections are listed, and custom providers can add their own nodes using `azdata.dataprotocol.registerObjectExplorerNodeProvider`. 

## Context Menu

### Extensions

Contributed through the [objectExplorer/item/context
](https://github.com/microsoft/azuredatastudio/wiki/Contribution-points#objectexploreritemcontext) contribution point in your package.json. See [context variables](https://github.com/microsoft/azuredatastudio/wiki/Context-Variables) for information on available variables to control visibility. 

### Core

Menu items are declared with `appendMenuItem` passing a `MenuId.ObjectExplorerItemContext` value as the MenuId. See [scripting.contribution](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/contrib/scripting/browser/scripting.contribution.ts) for examples. 

The [treeNodeContextKey](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/services/objectExplorer/common/treeNodeContextKey.ts), [connectionContextKey](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/services/connection/common/connectionContextKey.ts) and [serverInfoContextKey](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/services/connection/common/serverInfoContextKey.ts) are the main keys used control visibility of context menu items for the Servers view. 

# Data Explorer

This is a tree view that is declared with the [dataExplorer](https://github.com/microsoft/azuredatastudio/wiki/Contribution-points#dataexplorer) contribution point. You must also declare a `vscode.TreeDataProvider` to provider the tree items for the view. 

This is different from a normal TreeView in that it allows for displaying nodes from available providers (such as MSSQL) in Azure Data Studio. If you do not need this functionality then you should use a normal vscode.TreeView instead. 

To take advantage of this functionality the extension defining the Data Explorer tree will return an [azdata.TreeItem](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/azdata.d.ts#L2777) from the `TreeDataProvider.getChildren` call with the `childProvider` and `payload` properties set. This will cause a node from the provider specified by the `childProvider` property to be created, after which children of that node will be handled by the provider itself (through an [azdata.ObjectExplorerProvider](https://github.com/microsoft/azuredatastudio/blob/main/src/sql/azdata.d.ts#L1425)).

Example: 

![DataExplorer](https://user-images.githubusercontent.com/28519865/189247314-cb071eaa-470c-4fdc-89c3-67a25bfdbc48.png)

## Context Menu 

### Extensions

Contributed through the [dataExplorer/context](https://github.com/microsoft/azuredatastudio/wiki/Contribution-points#dataexplorercontext) contribution point in your package.json. 

### Core

Menu items are declared with `appendMenuItem` passing a `MenuId.DataExplorerContext` value as the MenuId. See [scripting.contribution](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/contrib/scripting/browser/scripting.contribution.ts) for examples. 

The [MssqlNodeContext](https://github.com/Microsoft/azuredatastudio/blob/main/src/sql/workbench/services/objectExplorer/browser/mssqlNodeContext.ts) context key controls visibility nodes contributed by the MSSQL provider. 