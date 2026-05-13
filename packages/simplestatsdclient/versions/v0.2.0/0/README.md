# Pure Nim async/sync library to send stats to StatsD compatible service

This library might be used either in sync or async mode.
It is now support buffering for sending multiple metrics at a time.

## Currently supported:
| Type                  | Status             |  Notes |
|----------------------:|:-------------------|:-------|
| timers                |       WORKING      |        |
| counters              |       WORKING      |        |
| sets                  |       WORKING      |        |
| gauge                 |       WORKING      |        |
| gauge increment       |       WORKING      |        |
| gauge decrement       |       WORKING      |        |


## Usage

`flush()` proc might be used in both buffered and unbuffered modes safely. In unbuffered mode it just does nothing.

### Sync
Unbuffered mode
```nim
import simplestatsdclient
let sender = newStatsDClient("localhost", Port(8125))
sender.counter("my.pretty.counter", 1)
sender.gauge("my.gauge", 19.5)
sender.increment("my.gauge", 2)
```

Buffered mode
```nim
import simplestatsdclient
let sender = newStatsDClient("localhost", Port(8125), buffered=true)
sender.counter("my.pretty.counter", 1)
sender.gauge("my.gauge", 19.5)
sender.increment("my.gauge", 2)
sender.flush()
```

### Async
Unbuffered mode
```nim
import std/[asyncdispatch]
import simplestatsdclient
let sender = newAsyncStatsDClient("localhost", Port(8125))
proc main() {.async.} =
    await sender.counter("my.pretty.counter", 1)
    await sender.gauge("my.gauge", 19.5)
    await sender.increment("my.gauge", 2)

waitFor(main())
```

Buffered mode
```nim
import std/[asyncdispatch]
import simplestatsdclient
let sender = newAsyncStatsDClient("localhost", Port(8125), buffered=true)
proc main() {.async.} =
    await sender.counter("my.pretty.counter", 1)
    await sender.gauge("my.gauge", 19.5)
    await sender.increment("my.gauge", 2)
    await sender.flush()

waitFor(main())
```
