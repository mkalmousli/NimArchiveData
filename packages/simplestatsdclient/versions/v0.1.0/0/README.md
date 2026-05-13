# Pure Nim async/sync library to send stats to StatsD compatible service

This library might be used either in sync or async mode.

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

### Sync
```nim
import simplestatsdclient
let sender = newStatsDClient("localhost", Port(8125))
sender.counter("my.pretty.counter", 1)
sender.gauge("my.gauge", 19.5)
sender.increment("my.gauge", 2)
```

### Async
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
