Azure Data Studio uses the same mechanism as VS Code for determining whether UI elements such as menus and buttons are enabled. For general information about what is available to use in `when` clauses, see see [when clause contracts](https://code.visualstudio.com/api/references/when-clause-contexts).

In addition to the built-in ones provided by VS Code, we have specific context around database connections available for extensions through `when` clauses in your `package.json`.

# Dashboard

In dashboard contributions we provide the following context variables: 

`connectionProvider` - A string of the identifier for the provider of the current connection. Ex. `connectionProvider == 'MSSQL'`.

`serverName` - A string of the server name of the current connection. Ex. `serverName == 'localhost'`.

`databaseName` - A string of the database name of the current connection. Ex. `databaseName == 'master'`.

`connection` - The full connection profile object for the current connection (IConnectionProfile)

`dashboardContext` - A string of the context of the page of the dashboard is currently on. Either 'database' or 'server'. Ex. `dashboardContext == 'database'`

# Server Info

For contexts that involve a server connection (such as Object Explorer) you can use the following variables. These values are from the [ServerInfo](https://github.com/microsoft/azuredatastudio/blob/main/src/sql/azdata.d.ts#L530) object for the connection, so they should only be used for providers that fill in these values for their server connections.

`serverMajorVersion` - A string containing the major version of the engine (`serverInfo.serverMajorVersion`). Default is undefined.

`isCloud` - A boolean that will be true if the connection is a cloud (Azure) connection, or false otherwise (`serverInfo.isCloud`). Default is false.

`engineEdition` - A string containing the engine edition ID of the instance (`serverInfo.engineEditionId`). Default is 0 (Unknown).