SQL Operations Studio provides an API that extensions can use to interact with other parts of the application such as Object Explorer. These APIs are available from the [`src/sql/sqlops.d.ts`](https://github.com/Microsoft/sqlopsstudio/blob/master/src/sql/sqlops.d.ts) file and are described below.

## Connection Management
`sqlops.connection`

_Available starting in version 0.26.7 (February Public Preview)_

Extensions can interact with connections using the `connection` namespace.

### Top-level Functions

- `getCurrentConnection(): sqlops.connection.Connection`
Gets the current connection based on the active editor or Object Explorer selection.

- `getActiveConnections(): sqlops.connection.Connection[]`
Gets a list of all the user's connections that are active. Returns an empty list if there are no such connections.

- `getCredentials(connectionId: string): { [name: string]: string }`
Gets a dictionary containing the credentials associated with a connection. These would otherwise be returned as part of the options dictionary under a `sqlops.connection.Connection` object but get stripped from that object. 

### `Connection`
- `options: { [name: string]: string }` The dictionary of connection options
- `providerName: string` The name of the connection provider (e.g. "MSSQL")
- `connectionId: string` The unique identifier for the connection

### Example Code
```
> let connection = sqlops.connection.getCurrentConnection();
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
> let credentials = sqlops.connection.getCredentials(connection.connectionId);
credentials: {
	password: ‘abc123’
}

```
