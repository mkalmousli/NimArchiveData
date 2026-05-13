# ic

IceCream-style debug printing for Nim with colored output, timestamps, and location info.

Inspired by Python's [icecream](https://github.com/gruns/icecream) library.

## Installation

```bash
nimble install https://this_github_url
```

## Usage

```nim
import ic

let x = 42
let name = "hello"
let data = @[1, 2, 3]

# Compile with -d:ic to enable output
ic x              # Green output
icr name          # Red output  
icb data          # Blue output
icy x + 1         # Yellow output

# Multiple args
ic x, name, data
```

Compile with `-d:ic` to enable:

```bash
nim c -d:ic myapp.nim
```

### Output Example

```
1736789234 | /path/to/file.nim:10 | 'x' | 42
1736789234 | /path/to/file.nim:11 | 'name' | hello
```

## Configuration

All configuration is done via compile-time defines:

| Flag | Description | Default |
|------|-------------|---------|
| `-d:ic` | Enable all ic macros | disabled |
| `-d:icg` | Enable only `ic` (green) | disabled |
| `-d:icr` | Enable only `icr` (red) | disabled |
| `-d:icb` | Enable only `icb` (blue) | disabled |
| `-d:icy` | Enable only `icy` (yellow) | disabled |
| `-d:ic.show_time=false` | Hide timestamp | true |
| `-d:ic.show_location=false` | Hide file:line | true |
| `-d:ic.show_variable=false` | Hide variable name | true |
| `-d:ic.show_value=false` | Hide value | true |
| `-d:ic.hrdt=true` | Human readable datetime | false |
| `-d:ic.prefix=MyApp` | Add custom prefix | "" |

### Example with options

```bash
nim c -d:ic -d:ic.hrdt=true -d:ic.prefix=DEBUG myapp.nim
```

Output:
```
DEBUG | 2024-01-13 3:45PM | /path/to/file.nim:10 | 'x' | 42
```

## Platform Support

- **Native**: Full color support via `std/terminal`
- **JavaScript**: Maps to `console.trace`, `console.error`, `console.info`, `console.warn`
- **NimScript**: Basic output without colors

### JavaScript-specific features

```nim
# Console grouping (JS only)
group:
  ic x
  ic y

group "My Group":
  ic data
```

## Ref Type Support

The package exports a `$` proc for `ref` types that handles nil safely:

```nim
type Foo = ref object
  value: int

var f: Foo = nil
ic f  # outputs: nil

f = Foo(value: 42)
ic f  # outputs: (value: 42)
```

If this conflicts with your own `$` overloads:

```nim
import ic except `$`
```

## License

MIT