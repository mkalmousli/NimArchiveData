# ic

IceCream-style debug printing for Nim. A debugging library inspired by Python's [icecream](https://github.com/gruns/icecream) that provides colored, informative debug output with variable names, values, timestamps, and source locations.

## Features

- **Colored output** - Green, red, blue, and yellow variants for visual differentiation
- **Zero-cost when disabled** - All macros compile to nothing when not enabled
- **Cross-platform** - Native, JavaScript, and NimScript support
- **Configurable** - Control what information appears in output via compile-time defines
- **Multiple arguments** - Print several variables in one call

## Installation

```bash
nimble install ic
```

## Quick Start

```nim
import ic

let x = 42
let name = "hello"

ic x           # Green output
icr name       # Red output
icb x, name    # Blue output (multiple args)
icy x + 1      # Yellow output (expressions work too)
```

Compile with `-d:ic` to see output:

```bash
nim c -r -d:ic myfile.nim
```

**Output:**
```
1736812345 | /path/to/myfile.nim:5 | 'x' | 42
1736812345 | /path/to/myfile.nim:6 | 'name' | hello
1736812345 | /path/to/myfile.nim:7 | 'x' | 42
1736812345 | /path/to/myfile.nim:7 | 'name' | hello
1736812345 | /path/to/myfile.nim:8 | 'x + 1' | 43
```

## Macros

| Macro | Color  | Enable Flag |
|-------|--------|-------------|
| `ic`  | Green  | `-d:ic` or `-d:icg` |
| `icr` | Red    | `-d:ic` or `-d:icr` |
| `icb` | Blue   | `-d:ic` or `-d:icb` |
| `icy` | Yellow | `-d:ic` or `-d:icy` |

## Configuration

All configuration is done via compile-time defines:

| Define | Default | Description |
|--------|---------|-------------|
| `-d:ic` | off | Enable all ic macros |
| `-d:icg` | off | Enable only `ic` (green) |
| `-d:icr` | off | Enable only `icr` (red) |
| `-d:icb` | off | Enable only `icb` (blue) |
| `-d:icy` | off | Enable only `icy` (yellow) |
| `-d:ic.show_time=BOOL` | true | Show timestamp |
| `-d:ic.show_location=BOOL` | true | Show file:line |
| `-d:ic.show_variable=BOOL` | true | Show variable/expression name |
| `-d:ic.show_value=BOOL` | true | Show the value |
| `-d:ic.hrdt=BOOL` | false | Human-readable datetime instead of unix timestamp |
| `-d:ic.prefix=STRING` | "" | Custom prefix for all output |

### Examples

Enable everything with human-readable timestamps:
```bash
nim c -r -d:ic -d:ic.hrdt=true myfile.nim
```

Minimal output (value only):
```bash
nim c -r -d:ic -d:ic.show_time=false -d:ic.show_location=false -d:ic.show_variable=false myfile.nim
```

Add a prefix for filtering logs:
```bash
nim c -r -d:ic -d:ic.prefix=DEBUG myfile.nim
```

Enable only red and yellow for errors/warnings:
```bash
nim c -r -d:icr -d:icy myfile.nim
```

## Platform Support

### Native (default)
Full color support via `std/terminal`. All features available.

### JavaScript (`-d:js`)
Maps to browser console methods:
- `ic` → `console.trace`
- `icr` → `console.error`
- `icb` → `console.info`
- `icy` → `console.warn`

Also provides `group` templates for console grouping:
```nim
group "My Group":
  ic someValue
  icb anotherValue
```

### NimScript
Basic echo-based output with location info. Works in `.nims` files and nimble tasks.

## Checkpoints

For simple checkpoint-style debugging, use the `blok` template:

```nim
import ic

blok "processing data":
  # your code here
  discard

# or use the cp()

let p = foo()

cp() # A checkpoint

let v = bar()

```

Compile with `-d:cp` to enable checkpoint output.

## Tips

- Use different colors to categorize your debug output (e.g., `icr` for errors, `icy` for warnings)
- The macros accept expressions, not just variables: `ic myList.len`, `ic a + b * c`
- Remove `-d:ic` from production builds for zero overhead
- Use `-d:ic.prefix` to tag output from specific modules

## License

MIT