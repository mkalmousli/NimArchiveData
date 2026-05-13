# Data Transfer Protocol for Nim

Modern networking interfaces for Nim.

## Data Transfer Protocol

The Data Transfer Protocol (DTP) is a larger project to make ergonomic network programming available in any language. See the full project [here](https://wkhallen.com/dtp/).

## Installation

Install the package:

```sh
$ nimble install nimdtp
```

Add the package in `<project>.nimble`:

```nim
requires "nimdtp == 0.1.0"
```

## Creating a server

A server can be built using the `Server` implementation:

```nim
import nimdtp/server

# Called when data is received from a client
proc receive(server: Server[int, string], clientId: int, data: string) =
  # Send back the length of the string
  asyncCheck server.send(clientId, data.len)

# Called when a client connects
proc connect(server: Server[int, string], clientId: int) =
  echo "Client with ID ", clientId, " connected"

# Called when a client disconnects
proc disconnect(server: Server[int, string], clientId: int) =
  echo "Client with ID ", clientId, " disconnected"

# Create a server that receives strings and returns the length of each string
let server = newServer[int, string](onReceive=receive, onConnect=connect, onDisconnect=disconnect)
waitFor server.start("127.0.0.1", 29275)
```

## Creating a client

A client can be built using the `Client` implementation:

```nim
import nimdtp/client

let message = "Hello, server!"

# Called when data is received from the server
proc receive(client: Client[string, int], data: int) =
  # Validate the response
  echo "Received response from server: ", data
  assert data == message.len

# Called when the client is disconnected from the server
proc disconnected(client: Client[string, int]) =
  echo "Unexpected disconnected from server"

# Create a client that sends a message to the server and receives the length of the message
let client = newClient(onReceive=receive, onDisconnected=disconnected)
waitFor client.connect("127.0.0.1", 29275)
# Send the message to the server
waitFor client.send(message)
```

## Security

Information security comes included. Every message sent over a network interface is encrypted with AES-256. Key exchanges are performed using a 2048-bit RSA key-pair.
