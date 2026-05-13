# bytesized

![Static Badge](https://img.shields.io/badge/%3E2.0.8-yellow?logo=nim&label=nim)
![GitLab Last Commit](https://img.shields.io/gitlab/last-commit/Maxb0tbeep%2Fbytesized?logo=git)
![Gitlab Pipeline Status](https://img.shields.io/gitlab/pipeline-status/Maxb0tbeep%2Fbytesized?logo=gitlab)
![Static Badge](https://img.shields.io/badge/nimble%20package%20-%20black?logo=nim&link=https%3A%2F%2Fnimble.directory%2Fpkg%2Fbytesized)

a nim library for manipulating data storage units so you don't have to write boring code.

## installation

`nimble add bytesized`

## usage

### Units enum

bytesized uses an enum called `Units` to represent different data storage units.

<br>

You can use `Bytes`, `KiB`, `KB`, `MiB`, `MB`, `GiB`, `GB`, `TiB`, `TB`, `PiB`, or `PB`

Just use `Units.GiB` or whatever unit you need

### toBytes

`toBytes` converts a float value of another unit to bytes.

```nim
proc toBytes(input: float, inUnit: Units): int
```

#### example

```nim
const kibibytes:float = 1.5

let bytes:int = kilobytes.toBytes(Units.KiB)
```

`bytes` equals 1536

---

### toUnit

`toUnit` is a umbrella term for all the procedures that convert one unit to another. It is very flexible and the easiest way to use bytesized.

<br>

It can be used with only an integer, for converting bytes to something else

```nim
proc toKiB(input: int): float
```

You can also specify the amount of decimal places to round to, otherwise, it defaults to 1 place

```nim
proc toMB(input:int, roundDigits:int): float
```

Another argument you can use is a `Units` enum value to tell the procedure which unit it's converting from.

However, this takes a `float` value for input since you won't be converting from bytes

```nim
proc toGiB(input:float, inUnit:Units): float
```

Or, you can use both at the same time

```nim
proc toTB(input:float, inUnit:Units, roundDigits:int): float
```

<br>

A full list of every `toUnit` procedure:
- toKiB
- toKB
- toMiB
- toMB
- toGiB
- toGB
- toTiB
- toTB
- toPiB
- toPB

They all work the same.

You can convert between kibibytes (1024) and kilobytes (1000), or any other units

#### example

```nim
const kibibytes:float = 1024.0

let kilobytes:float = kilobytes.toKB(Units.KiB)
```

`kilobytes` equals 1000.0

---

### convertBytes

`convertBytes` is a procedure that is called by each `toUnit` procedure. You can use it but it's just an alias, and less verbose.

```nim
proc convertBytes(input:float, inUnit:Units, outUnit:Units, roundDigits:int): float
```

You can skip the `roundDigits` argument to default to 1 decimal place

```nim
proc convertBytes(input:float, inUnit:Units, outUnit:Units): float
```

If no `inUnit` argument is specified, it assumes the input is in bytes.

```nim
proc convertBytes(input:float, outUnit:Units, roundDigits:int): float
```

```nim
proc convertBytes(input:int, outUnit:Units, roundDigits:int): float
```

```nim
proc convertBytes(input:float, outUnit:Units): float
```

```nim
proc convertBytes(input:int, outUnit:Units): float
```

#### example

```nim
const kibibytes:float = 16000000.0

let gibibytes:float = convertBytes(kibibytes, Units.KB, Units.GiB, 3)
```

`gibibytes` equals 14.901