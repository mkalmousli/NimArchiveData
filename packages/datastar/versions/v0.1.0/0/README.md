# datastar.nim

A Nim SDK for [Datastar](https://data-star.dev) - the hypermedia framework.

## Installation

```bash
nimble install datastar
```

## Usage

### Basic Example with std/asynchttpserver

```nim
import std/[asyncdispatch, asynchttpserver]
import datastar
import datastar/asynchttpserver as DATASTAR

proc handler(req: Request) {.async.} =
  let sse = await req.newSSEGenerator() ; defer: req.closeSSE()
  await sse.patchElements("<div id=\"hello\">Hello World</div>")

proc main() {.async.} =
  var server = newAsyncHttpServer()
  server.listen(Port(8080))
  while true:
    if server.shouldAcceptRequest():
      await server.acceptRequest(handler)
    else:
      await sleepAsync(100)

waitFor main()
```

### Patch Elements

```nim
# Basic element patch
await sse.patchElements("<div>Content</div>")

# With selector and mode
await sse.patchElements("<div>Content</div>", selector="#target", mode=Append)

# With view transitions
await sse.patchElements("<div>Content</div>", useViewTransition=true)
```

### Patch Signals

```nim
import std/json

# From JsonNode
await sse.patchSignals(%*{"count": 1, "name": "test"})

# Only if missing
await sse.patchSignals(%*{"count": 0}, onlyIfMissing=true)
```

### Execute Script

```nim
# Auto-remove after execution (default)
await sse.executeScript("console.log('hello')")

# Keep script in DOM
await sse.executeScript("window.myFunc = () => {}", autoRemove=false)

# With custom attributes
import std/tables
await sse.executeScript("...", attributes={"type": "module"}.toTable)
```

### Remove Elements

```nim
await sse.removeElements("#target")
await sse.removeElements(".items", useViewTransition=true)
```

### Remove Signals

```nim
await sse.removeSignals(@["count", "name"])
```

### Read Signals

```nim
# GET requests - extracts 'datastar' query parameter
let signals = readSignals(request.url.query)
echo signals["count"].getInt

# POST/PUT/etc - parse JSON body directly
let signals = readSignalsFromBody(request.body)

# Typed API - returns struct
type MySignals = object
  count: int
  name: string

let signals = readSignals(request.url.query, MySignals)
echo signals.count  # Typed access

# From POST body (typed)
let signals = readSignalsFromBody(request.body, MySignals)

# Custom deserializer (e.g., jsony)
import jsony
let signals = readSignals(request.url.query, proc(s: string): MySignals = s.fromJson(MySignals))
```

## Framework Adapters

### std/asynchttpserver

```nim
import datastar/asynchttpserver as datastarAsync

proc handler(req: Request) {.async.} =
  let sse = await req.newSSEGenerator()
  # ...
```

### Prologue

```nim
import datastar/prologue as datastarPrologue

proc handler(ctx: Context) {.async.} =
  let sse = await ctx.newSSEGenerator()
  # ...
```

## Examples

Run the counter example:

```bash
nim c -r examples/counter.nim
```

Then open http://localhost:8080 in your browser.

## Running Tests

The SDK includes golden tests that match the official Datastar SDK test suite.

```bash
# Run all tests
nim c -r test/test_golden.nim

# Or using nimble
nimble test
```

## License

MIT
