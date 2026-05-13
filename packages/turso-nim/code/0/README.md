# Turso Nim SDK

A powerful and feature-rich SDK for interacting with Turso databases in Nim. This SDK provides a seamless interface to Turso's distributed SQLite database with support for modern features like vector search, transactions, and parameterized queries.

## Features

- **Parameterized Queries**: Support for both positional and named parameters
- **Vector Search**: Built-in support for similarity searches using vector embeddings
- **Transaction Management**: Full transaction support with commit and rollback
- **Error Handling**: Robust error handling with detailed error messages
- **Interactive Queries**: Support for connection reuse with batons
- **Type Safety**: Strong type checking and proper error handling

## Quick Start

### Create HTTP Database URL
```bash
turso db show <database-name> --http-url
```

### Generate auth token
```bash
turso db tokens create <database-name>
```
See [Turso Docs](https://docs.turso.tech/introduction) for more information.

## Installation

```bash
nimble install https://codeberg.org/13thab/turso-nim
```

## Usage

### Import and Connect
```nim
import turso

var turso = connect("libsql://[databaseName]-[organizationName].turso.io", "<auth-token>")
```

### Basic Queries
```nim
# Execute a simple query
turso.execute("CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)")

# Query with positional parameters
turso.execute("INSERT INTO users (name) VALUES (?)", @[%*"John"])

# Query with named parameters
turso.execute("SELECT * FROM users WHERE name = :name", 
             named_args = %*{"name": "John"})

# Get query results
var data = turso.getData("SELECT * FROM users")
```

### Transaction Management
```nim
# Start a transaction
turso.startTransaction()

try:
  turso.execute("INSERT INTO users (name) VALUES (?)", @[%*"Alice"])
  turso.execute("UPDATE users SET name = ? WHERE name = ?", @[%*"Bob", %*"John"])
  turso.commit()
except:
  turso.rollback()
```

### Vector Search
```nim
# Perform similarity search
let results = turso.vectorSearch(
  "embeddings",           # table name
  "vector_column",        # column containing vectors
  @[0.1, 0.2, 0.3],      # query vector
  5                       # limit
)
```

### Response Structure
The `TursoResponse` object contains detailed information about the query execution:

```nim
type TursoResponse* = object
  cols*: seq[string]              # Column names
  rows*: seq[JsonNode]            # Row data
  affected_row_count*: int        # Number of affected rows
  last_insert_rowid*: int64       # Last inserted row ID
  replication_index*: string      # Replication status
  rows_read*: int                 # Number of rows read
  rows_written*: int              # Number of rows written
  query_duration_ms*: float       # Query execution time
```

### Error Handling
The SDK uses the `TursoError` type for error handling:

```nim
try:
  turso.execute("INSERT INTO users (name) VALUES (?)", @[%*"Alice"])
except TursoError as e:
  echo "Error: ", e.msg
```

## Cleanup

```nim
# Close the connection when done
turso.close()
```
