# humanize

Nim library for human-readable formatting of numbers, file sizes, times, durations, and lists. Inspired by Python [humanize](https://github.com/jmoiron/humanize) and Go [go-humanize](https://github.com/dustin/go-humanize).

- No external dependencies (only Nim stdlib)
- Explicit locale parameter on every function (no global state)
- 8 built-in locales: English, Arabic, German, Spanish, French, Italian, Russian, Chinese
- Nim >= 2.0.0

## Installation

```bash
nimble install humanize
```

## Quick Start

```nim
import humanize

echo numComma(1_000_000)         # "1,000,000"
echo ordinal(3)                  # "3rd"
echo naturalSize(1_500_000'i64)  # "1.5 MB"
echo naturalList(["a", "b", "c"]) # "a, b and c"
```

## Number Formatting

### ordinal

Convert an integer to its ordinal string representation.

```nim
import humanize

echo ordinal(1)   # "1st"
echo ordinal(2)   # "2nd"
echo ordinal(3)   # "3rd"
echo ordinal(11)  # "11th"
echo ordinal(21)  # "21st"
echo ordinal(111) # "111th"
```

With locales and gender:

```nim
import humanize
import humanize/lang/fr

echo ordinal(1, gMasc, LangFr)  # "1er"
echo ordinal(1, gFem, LangFr)   # "1re"
echo ordinal(2, locale = LangFr)     # "2e"
```

### numComma

Format a number with thousands separators.

```nim
import humanize

echo numComma(1000)       # "1,000"
echo numComma(1_000_000)  # "1,000,000"
echo numComma(-1234)      # "-1,234"

# Float overload with decimal places
echo numComma(1234567.89, 2)  # "1,234,567.89"
```

With a German locale:

```nim
import humanize
import humanize/lang/de

echo numComma(1_000_000, LangDe)  # "1.000.000"
```

### numWord

Convert large integers to a readable word form.

```nim
import humanize

echo numWord(1_000_000'i64)     # "1.0 million"
echo numWord(1_200_000'i64)     # "1.2 million"
echo numWord(1_000_000_000'i64) # "1.0 billion"
echo numWord(999_999'i64)       # "999,999" (below threshold)
```

### numName

Associated Press style: numbers 1-9 as words, others as digits.

```nim
import humanize

echo numName(1)   # "one"
echo numName(5)   # "five"
echo numName(9)   # "nine"
echo numName(10)  # "10"
echo numName(0)   # "0"
```

## File Sizes

### naturalSize

Format byte counts as human-readable file sizes. Always uses `"."` as decimal separator (international units, no locale dependency).

```nim
import humanize

# SI (decimal, powers of 1000)
echo naturalSize(0'i64)             # "0 Bytes"
echo naturalSize(1'i64)             # "1 Byte"
echo naturalSize(1_000'i64)         # "1.0 KB"
echo naturalSize(1_000_000'i64)     # "1.0 MB"
echo naturalSize(1_000_000_000'i64) # "1.0 GB"

# Binary (powers of 1024)
echo naturalSize(1024'i64, binary = true)      # "1.0 KiB"
echo naturalSize(1_048_576'i64, binary = true)  # "1.0 MiB"

# GNU style (single-letter, powers of 1024)
echo naturalSize(1_048_576'i64, gnu = true)  # "1.0M"

# Custom format
echo naturalSize(1_234_567'i64, format = "%.2f")  # "1.23 MB"

# Negative values
echo naturalSize(-1000'i64)  # "-1.0 KB"
```

### parseSize

Parse a human-readable size string back to bytes.

```nim
import humanize

echo parseSize("1 MB")              # 1000000
echo parseSize("1 MiB")             # 1048576
echo parseSize("1.5 GB")            # 1500000000
echo parseSize("1M", binary = true) # 1048576
echo parseSize("100 bytes")         # 100
```

Raises `ValueError` on invalid input.

### Byte Constants

Convenient constants for size calculations:

```nim
import humanize

# SI (powers of 1000)
const size = 5 * GigaByte  # 5_000_000_000

# Binary (powers of 1024)
const mem = 2 * GibiByte   # 2_147_483_648
```

Available: `Byte`, `KiloByte`, `MegaByte`, `GigaByte`, `TeraByte`, `PetaByte`, `ExaByte`, `KibiByte`, `MebiByte`, `GibiByte`, `TebiByte`, `PebiByte`, `ExbiByte`.

## Time

### naturalDelta

Express a duration as a human-readable delta.

```nim
import std/times
import humanize

echo naturalDelta(initDuration(seconds = 1))    # "1 second"
echo naturalDelta(initDuration(seconds = 45))   # "45 seconds"
echo naturalDelta(initDuration(seconds = 120))  # "2 minutes"
echo naturalDelta(initDuration(hours = 3))      # "3 hours"
echo naturalDelta(initDuration(days = 15))      # "15 days"
echo naturalDelta(initDuration(days = 400))     # "1 year, 1 month"

# Without month grouping
echo naturalDelta(initDuration(days = 400), months = false)
# "1 year, 35 days"

# Int overload (seconds)
echo naturalDelta(3600)  # "1 hour"
```

### naturalTime

Express a DateTime relative to now.

```nim
import std/times
import humanize

let base = now()
let past = base - initDuration(hours = 2)
let future = base + initDuration(days = 3)

echo naturalTime(base, base)      # "just now"
echo naturalTime(past, base)      # "2 hours ago"
echo naturalTime(future, base)    # "3 days from now"
```

### naturalDay / naturalDate

Relative day names, with optional year.

```nim
import std/times
import humanize

let today = now()
let yesterday = today - initDuration(days = 1)
let tomorrow = today + initDuration(days = 1)

echo naturalDay(today, today)      # "today"
echo naturalDay(yesterday, today)  # "yesterday"
echo naturalDay(tomorrow, today)   # "tomorrow"

# naturalDate includes year for other years
let oldDate = dateTime(2023, mMar, 15, 0, 0, 0, 0, utc())
echo naturalDate(oldDate, today)   # "Mar 15, 2023"
```

## Durations

### preciseDelta

Multi-unit, precise duration formatting.

```nim
import std/times
import humanize

echo preciseDelta(initDuration(seconds = 90))
# "1 minute and 30 seconds"

echo preciseDelta(initDuration(seconds = 3633))
# "1 hour and 33 seconds"

echo preciseDelta(initDuration(days = 1, seconds = 30))
# "1 day and 30 seconds"

# Control minimum unit
echo preciseDelta(initDuration(seconds = 3633), minimumUnit = duMinutes)
# "1 hour and 60.55 minutes"

# Suppress specific units
echo preciseDelta(initDuration(days = 10), suppress = @[duWeeks])
# "10 days"

# Int overload (seconds)
echo preciseDelta(90)  # "1 minute and 30 seconds"
```

## Lists

### naturalList

Join strings in a human-readable way.

```nim
import humanize

echo naturalList(newSeq[string]())  # ""
echo naturalList(["a"])             # "a"
echo naturalList(["a", "b"])        # "a and b"
echo naturalList(["a", "b", "c"])   # "a, b and c"
```

## Locales

Every function accepts a `locale` parameter (always the last argument, defaults to English). Locales are imported explicitly:

```nim
import humanize
import humanize/lang/de
import humanize/lang/ru

echo numComma(1_000_000, LangDe)  # "1.000.000"
echo naturalDelta(initDuration(seconds = 30), locale = LangRu)
# "30 секунд"
echo naturalList(["a", "b", "c"], LangDe)
# "a, b und c"
```

### Available Locales

| Import | Constant | Language |
|--------|----------|----------|
| *(built-in)* | `LangEn` | English |
| `humanize/lang/ar` | `LangAr` | Arabic |
| `humanize/lang/de` | `LangDe` | German |
| `humanize/lang/es` | `LangEs` | Spanish |
| `humanize/lang/fr` | `LangFr` | French |
| `humanize/lang/it` | `LangIt` | Italian |
| `humanize/lang/ru` | `LangRu` | Russian |
| `humanize/lang/zh` | `LangZh` | Chinese |

### Custom Locale

You can define your own locale by constructing a `Locale` object:

```nim
import humanize

const MyLocale = Locale(
  name: "custom",
  pluralRule: prGermanic,
  ordinalRule: orEnglish,
  timeUnits: TimeUnits(
    years: Plurals(one: "year", few: "years", many: "years"),
    # ... fill all fields
  ),
  conjunction: "and",
  serialComma: true,  # Oxford numComma
  # ... other fields
)

echo naturalList(["a", "b", "c"], MyLocale)
# "a, b, and c"  (note the Oxford numComma)
```

## Selective Imports

Import only what you need:

```nim
# Only number formatting
import humanize/number

# Only file sizes (no locale dependency)
import humanize/filesize

# Only list formatting
import humanize/list
```

## API Reference

### Number Module (`humanize/number`)

| Proc | Description |
|------|-------------|
| `ordinal(n, gender?, locale?)` | `ordinal(3)` -> `"3rd"` |
| `numComma(n, locale?)` | `numComma(1000)` -> `"1,000"` |
| `numComma(n, ndigits?, locale?)` | `numComma(1234.5, 2)` -> `"1,234.50"` |
| `numWord(n, locale?)` | `numWord(1_000_000)` -> `"1.0 million"` |
| `numName(n, locale?)` | `numName(5)` -> `"five"` |

### File Size Module (`humanize/filesize`)

| Proc | Description |
|------|-------------|
| `naturalSize(bytes, binary?, gnu?, format?)` | `naturalSize(1_000_000)` -> `"1.0 MB"` |
| `parseSize(text, binary?)` | `parseSize("1.5 GB")` -> `1_500_000_000` |

### Time Module (`humanize/time`)

| Proc | Description |
|------|-------------|
| `naturalDelta(duration, months?, locale?)` | `naturalDelta(initDuration(seconds=120))` -> `"2 minutes"` |
| `naturalTime(dt, now?, months?, locale?)` | `naturalTime(past)` -> `"2 hours ago"` |
| `naturalDay(dt, now?, locale?)` | `naturalDay(yesterday)` -> `"yesterday"` |
| `naturalDate(dt, now?, locale?)` | `naturalDate(oldDate)` -> `"Mar 15, 2023"` |

### Duration Module (`humanize/duration`)

| Proc | Description |
|------|-------------|
| `preciseDelta(duration, minimumUnit?, suppress?, format?, locale?)` | `preciseDelta(initDuration(seconds=3633))` -> `"1 hour and 33 seconds"` |

### List Module (`humanize/list`)

| Proc | Description |
|------|-------------|
| `naturalList(items, locale?)` | `naturalList(["a","b","c"])` -> `"a, b and c"` |

## License

MIT
