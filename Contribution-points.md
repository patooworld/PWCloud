This document covers the various contribution points that are defined in the package.json extension manifest. These are in addition to the [ones inherited by VS Code](https://vscode-westeu.azurewebsites.net/api/references/contribution-points).

# connectionProvider

Defines a connection provider, which is added the list of connections a user can create a connection profile from. The extension defining this must also register a connection provider using `azdata.dataprotocol.registerConnectionProvider` to handle actually making the connections.

## connectionProvider.providerId

type: `string`

The connection provider id, e.g. MSSQL, LOGANALYTICS

## connectionProvider.displayName

type: `string`

The display name of the connection provider, e.g. Microsoft SQL Server, Azure Monitor Logs

## connectionProvider.iconPath

type: `string` or `object`

Path to the connection provider's icon

### connectionProvider.iconPath.id

type: `string`

### connectionProvider.iconPath.path

type: `object`

#### connectionProvider.iconPath.path.light

type: `string`

#### connectionProvider.iconPath.path.dark

type: `string`

### connectionProvider.iconPath.default

type: `boolean`

## connectionprovider.useFullOptions

type: `boolean`

Whether or not to use all non-default advanced options to create connection URI that uniquely identifies a connection. This configuration is set to 'false' by default. By default, only options specified with a 'specialValueType' are considered for creating connection URI.

## connectionProvider.azureResource

type: `string`

Azure resource endpoint to be used by the connection provider. Defaults to `Sql` if not specified.

## connectionProvider.notebookKernelAlias

type: `string`

Alias to be used for the kernel in notebooks

## connectionProvider.connectionStringOptions

type: `object`

Connection string options for the connection provider

### connectionProvider.connectionStringOptions.isEnabled

type: `boolean`

Whether the connection provider supports connection string as an input option. The default value is false.

### connectionProvider.connectionStringOptions.isDefault

type: `boolean`

Whether the connection provider uses connection string as the default option to connect. The default value is false.

## connectionProvider.isQueryProvider

type: `boolean`

Boolean indicating whether the connection provider supports queries. The default value is true.

## connectionProvider.isExecutionPlanProvider

type: `boolean`

Boolean indicating whether the connection provider supports execution plan.

## connectionProvider.supportedExecutionPlanFileExtensions

type: `array`

List of file extensions supported by the execution plan provider, if execution plan is supported.

## connectionProvider.supportCopyResultsToClipboard

type: `boolean`

Boolean indicating whether the provider support copy results to clipboard. Default value is false. If true, the copy results to clipboard will be delegated to the provider to avoid passing large amount of data using the RPC channel, otherwise ADS will handle the copy request on the UI side.

## connectionProvider.serverConnectionIdName

type: `string`

The server defined name of the ID assigned to all connections (such as editors or object explorer nodes) if it is supported by the provider, such as PID used by SQL Server, this is used for display in areas such as Query Editor where knowing the process ID is needed.

## connectionProvider.connectionOptions

type: `array`

An array of objects with the following properties that define the connection options to display in the Connection Dialog.

### connectionProvider.connectionOptions.name

type: `string`

### connectionProvider.connectionOptions.displayName

type: `string`

### connectionProvider.connectionOptions.description

type: `string`

### connectionProvider.connectionOptions.groupName

type: `string`

### connectionProvider.connectionOptions.valueType

type: `enum`

Either `string`, `multistring`, `password`, `number`, `category`, `boolean` or `object`

### connectionProvider.connectionOptions.specialValueType

type: `enum`

Either `connectionName`, `serverName`, `databaseName`, `authType`, `userName`, `password` or `appName`

### connectionProvider.connectionOptions.defaultValue

type: `string`, `number`, `boolean`, `object`, `integer`, `null` or `array`

### connectionProvider.connectionOptions.defaultValueOsOverrides

type: `array`

#### connectionProvider.connectionOptions.defaultValueOsOverrides.os

type: `enum`

Either `Windows`, `Macintosh` or `Linux`

#### connectionProvider.connectionOptions.defaultValueOsOverrides.defaultValueOverride

type: `string`, `number`, `boolean`, `object`, `integer`, `null` or `array`

### connectionProvider.connectionOptions.objectType

type: `string`

### connectionProvider.connectionOptions.categoryValues

type: `array`

#### connectionProvider.connectionOptions.categoryValues.name

type: `string`

#### connectionProvider.connectionOptions.categoryValues.displayName

type: `string`

### connectionProvider.connectionOptions.showOnConnectionDialog

type: `boolean`

When set to true, the respective connection option will be rendered on the main connection dialog and not the Advanced Options window.

### connectionProvider.connectionOptions.placeholder

type: `string`

The placeholder text to show in option control when it's value is empty.

### connectionProvider.connectionOptions.onSelectionChange

type: `array`

Used to define list of values based on which another option is rendered visible/hidden.

#### connectionProvider.connectionOptions.onSelectionChange.values

type: `array`

Values that affect actions defined in this event.

#### connectionProvider.connectionOptions.onSelectionChange.dependentOptionActions

type: `array`

Action to be taken on another option when selected value matches to the list of values provided.

##### connectionProvider.connectionOptions.onSelectionChange.dependentOptionActions.optionName

type: `string`

Name of option affected by defined action.

##### connectionProvider.connectionOptions.onSelectionChange.dependentOptionActions.action

type: `enum`

Action to be taken. Either `show` or `hide`.

##### connectionProvider.connectionOptions.onSelectionChange.dependentOptionActions.required

type: `boolean`

Whether or not the option should be set to required when visible. Defaults to false.

### connectionProvider.connectionOptions.isIdentity

type: `boolean`

### connectionProvider.connectionOptions.isRequired

type: `boolean`

### connectionProvider.connectionOptions.isArray

type: `boolean`

# dataExplorer

This is an array of `dataExplorer` contributions, each with the following properties. This is used to add views to the `Connections` view container in the panel - it currently doesn't support having views adding through the normal [contributes.views](https://code.visualstudio.com/api/references/contribution-points#contributes.views) extension point from VS Code.

## dataExplorer.id

The ID of the view - used when registering the associated `vscode.TreeDataProvider`

## dataExplorer.name

The human-readable name of the view shown in the title bar

## dataExplorer.when

The when-clause condition for when the view will be shown

# menus

These are custom menu contributions for Azure Data Studio, in addition to the built-in ones inherited from VS Code. 

## dataExplorer/context

Menu items for Data Explorer contributed views. 

## objectExplorer/item/context

Menu items for the Object Explorer (Server) tree view
### Default menu item
To set an item as the default menu item when a node is double-clicked, you can set the `isDefault` property to `true`. An error will be logged to the console if multiple menu items are set as the default action for a node and none of them will be executed.

Example: 
```
        {
          "command": "mssql.objectProperties",
          "when": "connectionProvider == MSSQL && nodeType =~ /^(ServerLevelLogin|User)$/ && config.workbench.enablePreviewFeatures",
          "group": "0_query@1",
          "isDefault": true
        }
```

# dashboard
Contribute tab, container, insight widget to the dashboard.
<img src='./media/dashboard_page.png'>

## dashboard.tabs

dashboard.tabs will create the tab sections inside the dashboard page. It expects an object or an array of objects.  

```
"dashboard.tabs": [
	{
		"id": "test-tab1",
		"title": "Test 1",
		"description": "The test 1 displays a list of widgets.",
		"when": "connectionProvider == 'MSSQL' && !mssql:iscloud",
		"alwaysShow": true,
		"container": {
			…
		}
	}
]
```

## dashboard.containers

Instead of specifying dashboard container inline (inside the dashboard.tab). You can register containers using dashboard.containers. It accepts an object or an array of the object.

```
"dashboard.containers": [
	{
		"id": "innerTab1",
		"container": {
			…
		}
	},
	{
		"id": "innerTab2",
		"container": {
			…
		}
	}
]
```

To refer to registered container, you can simply specify the id of the container

```
"dashboard.tabs": [
	{
		...
		"container": {
			"innerTab1": {             
			}
		}
	}
]

```

## dashboard.insights

You can register insights using dashboard.insights. This is similiar to [Tutorial: Build a custom insight widget](https://docs.microsoft.com/en-us/sql/sql-operations-studio/tutorial-build-custom-insight-sql-server)

```
"dashboard.insights": {
	"id": "my-widget"
	"type": {
		"count": {
			"dataDirection": "vertical",
			"dataType": "number",
			"legendPosition": "none",
            "labelFirstColumn": false,
			"columnsAsLabels": false
        }
    },
    "queryFile": "{your file folder}/activeSession.sql"
}
```


### Dashboard container types

There are 4 different container types that we currently support:

1. `widgets-container`

	<img src='./media/widgets_container.png'>
	The list of widgets that will be displayed in the container. It’s a flow layout. It accepts the list of widgets.

	```
	"container": {
		"widgets-container": [
			{
				"widget": {
					"query-data-store-db-insight": {
					}
				}
			},
			{
				"widget": {
					"explorer-widget": {
					}
				}
			}
		]
	}
	```
	
2. 	`webview-container`

	<img src='./media/webview_container.png'>
	The webview will be displayed in the entire container. It expects webview id to be the same is tab ID

	```
	"container": {
		"webview-container": null
	}
	```
	
3. 	`grid-container`

	<img src='./media/grid_container.png'>
	The list of widgets or webviews that will be displayed in the grid layout

	```
	"container": {
		"grid-container": [
			{
				"name": "widget 1",
				"widget": {
					"explorer-widget": {
					}
				},
				"row":0,
				"col":0
			},
			{
				"name": "widget 2",
				"widget": {
					"tasks-widget": {
						"backup", 
						"restore",
						"configureDashboard",
						"newQuery"
					}
				},
				"row":0,
				"col":1
			},
			{
				"name": "Webview 1",
				"webview": {
					"id": "google"
				},
				"row":1,
				"col":0,
				"colspan":2
			},
			{
				"name": "widget 3",
				"widget": {
					"explorer-widget": {}
				},
				"row":0,
				"col":3,
				"rowspan":2
			},
		]
	```

4. 	`nav-section`

	<img src='./media/nav_section.png'>
	The navigation section will be displayed in the container

	```
	"container": {
		"nav-section": [
			{
				"id": "innerTab1",
				"title": "inner-tab1",
				"icon": {
					"light": "./icons/tab1Icon.svg",
					"dark": "./icons/tab1Icon_dark.svg"
				}
				"container": {
					…
				}
			},
			{
				"id": "innerTab2",
				"title": "inner-tab2",
				"icon": {
					"light": "./icons/tab2Icon.svg",
					"dark": "./icons/tab2Icon_dark.svg"
				}
				"container": {
					…
				}
			}
		]
	}
	```