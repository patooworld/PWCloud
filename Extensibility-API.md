Azure Data Studio provides an API that extensions can use to interact with other parts of the application such as Object Explorer. These APIs are available from the [`src/sql/azdata.d.ts`](https://github.com/Microsoft/azuredatastudio/blob/master/src/sql/azdata.d.ts) file and some samples are given below. Stable APIs will generally not be removed or have breaking changes made without releasing them as 'deprecated' first. Proposed APIs are also available as discussed below and are subject to change at any time.

## Proposed APIs
We have added proposed APIs to allow extensions to display custom UI in dialogs, wizards, and document tabs, among other capabilities. See the [proposed API types file](https://github.com/Microsoft/azuredatastudio/blob/master/src/sql/azdata.proposed.d.ts) for more documentation, though be aware that these APIs are subject to change at any time. Examples of how to use some of these APIs can be found in the ["sqlservices" sample extension](https://github.com/Microsoft/azuredatastudio/tree/master/samples/sqlservices).

## Sample - Connection Management
`azdata.connection`
```
> let connection = azdata.connection.getCurrentConnection();
connection: {
	providerName: ‘MSSQL’,
	connectionId: ‘d97bb63a-466e-4ef0-ab6f-00cd44721dcc’,
	options: {
		server: ‘mairvine-sql-server’,
		user: ‘sa’,
		authenticationType: ‘sqlLogin’,
		…
	},
	…
}
> let credentials = azdata.connection.getCredentials(connection.connectionId);
credentials: {
	password: ‘abc123’
}

```

## Object Explorer
`azdata.objectexplorer`
Also see the [list of types that Azure Data Studio supports by default](https://github.com/Microsoft/azuredatastudio/wiki/Object-Explorer-types-supported-by-FindNodes-API) for the `findNodes` API.
```
private async interactWithOENode(selectedNode: azdata.objectexplorer.ObjectExplorerNode): Promise<void> {
	let choices = ['Expand', 'Collapse', 'Select', 'Select (multi)', 'Deselect', 'Deselect (multi)'];
	if (selectedNode.isLeaf) {
		choices[0] += ' (is leaf)';
		choices[1] += ' (is leaf)';
	} else {
		let expanded = await selectedNode.isExpanded();
		if (expanded) {
			choices[0] += ' (is expanded)';
		} else {
			choices[1] += ' (is collapsed)';
		}
	}
	let parent = await selectedNode.getParent();
	if (parent) {
		choices.push('Get Parent');
	}
	let children = await selectedNode.getChildren();
	children.forEach(child => choices.push(child.label));
	let choice = await vscode.window.showQuickPick(choices);
	let nextNode: azdata.objectexplorer.ObjectExplorerNode = undefined;
	if (choice === choices[0]) {
		selectedNode.setExpandedState(vscode.TreeItemCollapsibleState.Expanded);
	} else if (choice === choices[1]) {
		selectedNode.setExpandedState(vscode.TreeItemCollapsibleState.Collapsed);
	} else if (choice === choices[2]) {
		selectedNode.setSelected(true);
	} else if (choice === choices[3]) {
		selectedNode.setSelected(true, false);
	} else if (choice === choices[4]) {
		selectedNode.setSelected(false);
	} else if (choice === choices[5]) {
		selectedNode.setSelected(false, true);
	} else if (choice === 'Get Parent') {
		nextNode = parent;
	} else {
		let childNode = children.find(child => child.label === choice);
		nextNode = childNode;
	}
	if (nextNode) {
		let updatedNode = await azdata.objectexplorer.getNode(nextNode.connectionId, nextNode.nodePath);
		this.interactWithOENode(updatedNode);
	}
}

vscode.commands.registerCommand('mssql.objectexplorer.interact', () => {
	azdata.objectexplorer.getActiveConnectionNodes().then(activeConnections => {
		vscode.window.showQuickPick(activeConnections.map(connection => connection.label + ' ' + connection.connectionId)).then(selection => {
			let selectedNode = activeConnections.find(connection => connection.label + ' ' + connection.connectionId === selection);
			this.interactWithOENode(selectedNode);
		});
	});
});
```