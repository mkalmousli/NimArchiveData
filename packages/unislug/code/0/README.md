# unislug

Nim library for generating URL-safe slugs from Unicode strings with transliteration support.

Analogue of [python-slugify](https://github.com/un33k/python-slugify), [gosimple/slug](https://github.com/gosimple/slug) (Go), [cocur/slugify](https://github.com/cocur/slugify) (PHP).

## Installation

```bash
nimble install unislug
```

## Quick start

```nim
import unislug

echo slugify("Hello, World!")        # "hello-world"
echo slugify("Привет, мир!")         # "privet-mir"
echo slugify("Ärger mit Übung")      # "aerger-mit-uebung"
echo slugify("Crème brûlée")         # "creme-brulee"
```

## Options

```nim
# Custom separator
echo slugify("Hello World", separator = "_")
# "hello_world"

# Keep original case
echo slugify("Hello World", lowercase = false)
# "Hello-World"

# Limit length (cuts on word boundary)
echo slugify("one two three four five", maxLength = 13)
# "one-two-three"

# Disable transliteration
echo slugify("Привет мир", transliterate = false)
# ""

# Custom replacements (applied before transliteration)
echo slugify("C++", customReplacements = @[("++", "pp")])
# "cpp"
```

## SlugifyOptions

For full control, pass a `SlugifyOptions` object:

```nim
import unislug

let opts = SlugifyOptions(
  separator: "_",
  lowercase: true,
  maxLength: 50,
  transliterate: true,
  customReplacements: @[("&", "and"), ("@", "at")],
)

echo slugify("Tom & Jerry @ Home", opts)
# "tom_and_jerry_at_home"
```

### Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `separator` | `string` | `"-"` | Separator between words |
| `lowercase` | `bool` | `true` | Convert result to lowercase |
| `maxLength` | `int` | `0` | Max slug length (0 = no limit). Cuts on word boundary when possible |
| `transliterate` | `bool` | `true` | Transliterate Unicode characters to ASCII |
| `customReplacements` | `seq[tuple[src, dst: string]]` | `@[]` | Custom string replacements applied before processing |

## Supported scripts

- **Cyrillic** — Russian, Ukrainian
- **Latin diacritics** — French, German, Spanish, Polish, Czech/Slovak, Turkish, Romanian, Hungarian
- **Special characters** — `&` → and, `@` → at, `$` → dollar, `€` → eur, `£` → lb, `¥` → yen, etc.

## Requirements

- Nim >= 2.0.0
- No external dependencies (stdlib only)

## License

MIT
