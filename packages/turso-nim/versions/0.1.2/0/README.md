# Turso Nim SDK

This is the Turso SDK for the Nim programming language. It is a work in progress and is not yet ready for use.

## Quick Start

### Create HTTP Database URL
```bash
turso db show <database-name> --http-url
```

### Generate auth token. And copy it somewhere.
```bash
turso db tokens create <database-name>
```
See [Turso Docs](https://docs.turso.tech/introduction) for more information.

## Use the SDK

### Install the SDK
```bash
nimble install https://codeberg.org/13thab/turso-nim
```

### Import the SDK
```nim
import turso
```

### Create a client
```nim
var turso = connect("libsql://[databaseName]-[organizationName].turso.io", "<auth-token>")
```
### Execute a query
```nim
turso.execute("CREATE TABLE [table] (id INT, name TEXT)")
```
### Get data
```nim
var data = turso.getData("SELECT * FROM [table]")
```
### Result data
```json
{
  "cols": [],
  "rows": [],
  "affected_row_count": 0,
  "last_insert_rowid": null,
  "replication_index": "16"
}
```
The response is a JSON object. The `results` array contains the results of the query. The `response` object contains the response type and the result of the query.
