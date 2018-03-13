For general information about context in vscode and subsequently sqlops see [Extensibility](https://code.visualstudio.com/docs/extensionAPI/extension-points#_example).

In Sqlops we have specific context around database connections available for extensions.

#Dashboard
In dashboard we provide the following context variables.

`connectionProvider` - A string of the identifier for the provider of the current connection. Ex. `connectionProvider == 'MSSQL'`.

`serverName` - A string of the server name of the current connection. Ex. `serverName == 'localhost'`.

`databaseName` - A string of the database name of the current connection. Ex. `databaseName == 'master'`.

`connection` - The full connection profile object for the current connection (IConnectionProfile)

`dashboardContext` - A string of the context of the page of the dashboard is currently on. Either 'database' or 'server'. Ex. `dashboardContext == 'database'`