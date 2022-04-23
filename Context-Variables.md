Azure Data Studio uses the same mechanism as VS Code for determining whether UI elements such as menus and buttons are enabled. For general information about what is available to use in `when` clauses, see see [when clause contracts](https://code.visualstudio.com/api/references/when-clause-contexts).

In addition to the built-in ones provided by VS Code, we have specific context around database connections available for extensions through `when` clauses in your `package.json`.

# Dashboard
In dashboard contributions we provide the following context variables: 

`connectionProvider` - A string of the identifier for the provider of the current connection. Ex. `connectionProvider == 'MSSQL'`.

`serverName` - A string of the server name of the current connection. Ex. `serverName == 'localhost'`.

`databaseName` - A string of the database name of the current connection. Ex. `databaseName == 'master'`.

`connection` - The full connection profile object for the current connection (IConnectionProfile)

`dashboardContext` - A string of the context of the page of the dashboard is currently on. Either 'database' or 'server'. Ex. `dashboardContext == 'database'`