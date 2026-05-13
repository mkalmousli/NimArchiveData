# BatMon

A simple battery monitor tool that can notify you on battery status changes for FreeBSD.  
Inspired by but in no way similar to [batsignal](https://github.com/electrickite/batsignal) - inspiring the basic function only.
Written in pure [Nim](https/nim-lang.org).

## Requirements

### Run Requirements
* `apm`
* `notify-send`

### Build Requirements
* `nim`
* `nimble`

## Installation
To install into your `.nimble/bin` directory
```sh
git clone https://codeberg.org/pswilde/batmon && cd batmon
nimble install
```

## Running
```sh
# To run the daemon server notifier, just run:
batmon -d

# To run once and just show battery level, run:
batmon -o
```

## Using
When importing Batmon as a module you have access to the `get_battery_status()` 
procedure which will return a `Battery` object you can use elsewhere.

### Battery Type
```nim
type 
  Battery* = object
    status*: Status
    charge*: float
  Status* = enum
    High,
    Low,
    Critical,
    Charging,
    Unknown
```

Also, you have access to the notification handler module, where you can build 
and send your own notifications:

```nim
var n = newNotification("Title", "Body", urgency = Normal , timeout = 5000)
discard n.send()
```

## ToDo
[x] Add similar functionality for Linux systems
[ ] Use libnotify instead of notify-send
