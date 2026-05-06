# json2schema

> Infer a **JSON Schema** ([draft 2020-12](https://json-schema.org/specification)) from any JSON value.  
> No real data values are ever copied into the output — only structural and type information.

---

## Features

- Supports all JSON primitive types: `null`, `boolean`, `integer`, `number`, `string`
- Recursively handles nested **objects** and **arrays**
- Smart array-item merging:
  - Homogeneous arrays → single `items` schema
  - Mixed-type arrays → `anyOf` with structurally deduplicated variants
- All object properties are marked as **`required`** and `additionalProperties` is set to `false`
- Emits `$schema: https://json-schema.org/draft/2020-12/schema`
- Zero runtime dependencies beyond the Nim standard library
- Works as a **library** (`json2schemalib`) or a **CLI tool** (`json2schema`)

---

## Project layout

```
json2schema/
├── src/
│   ├── json2schemalib.nim   # Core library (importable)
│   └── json2schema.nim      # CLI entry point
├── tests/
│   └── test_all.nim         # Full test suite (unittest)
├── examples/
│   ├── user.json            # Example: nested user object
│   ├── products.json        # Example: array of product objects
│   └── mixed.json           # Example: mixed types and structures
├── json2schema.nimble        # Nimble package descriptor
└── README.md
```

---

## Requirements

| Tool | Minimum version |
|------|----------------|
| Nim  | 1.6.0          |

No third-party packages are needed.

---

## Building

### With Nimble (recommended)

```bash
nimble build
```

The binary is placed at `bin/json2schema` (or `bin/json2schema.exe` on Windows).

### Manually

```bash
nim compile --opt:speed src/json2schema.nim
```

---

## CLI usage

```
json2schema <input.json> [output.json]
json2schema -                           # read from stdin
```

| Argument | Description |
|----------|-------------|
| `input.json` | Path to the source JSON file |
| `output.json` | (optional) Destination for the schema. Omit to print to stdout. |
| `-` | Read JSON from stdin |
| `-h`, `--help` | Show help and exit |

### Examples

```bash
# Print schema to stdout
json2schema examples/user.json

# Write schema to a file
json2schema examples/user.json user.schema.json

# Pipe from stdin
echo '{"name":"Alice","age":30}' | json2schema -

# Chain with jq
cat examples/products.json | json2schema - | jq '.properties'
```

---

## Example input / output

**Input** (`examples/user.json`):

```json
{
  "id": 1,
  "username": "alice",
  "active": true,
  "score": 9.75,
  "roles": ["admin", "user"],
  "address": { "city": "Springfield", "zip": "12345" },
  "metadata": null
}
```

**Output** (inferred schema — no real values):

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "id":       { "type": "integer" },
    "username": { "type": "string" },
    "active":   { "type": "boolean" },
    "score":    { "type": "number" },
    "roles": {
      "type": "array",
      "items": { "type": "string" }
    },
    "address": {
      "type": "object",
      "properties": {
        "city": { "type": "string" },
        "zip":  { "type": "string" }
      },
      "required": ["city", "zip"],
      "additionalProperties": false
    },
    "metadata": { "type": "null" }
  },
  "required": ["id", "username", "active", "score", "roles", "address", "metadata"],
  "additionalProperties": false
}
```

### Mixed-type arrays

When an array contains elements of different types, `anyOf` is used:

```json
// Input
{ "readings": [42, 3.14, "N/A", null] }

// Output (items portion)
"items": {
  "anyOf": [
    { "type": "integer" },
    { "type": "number" },
    { "type": "string" },
    { "type": "null" }
  ]
}
```

---

## Using as a library

```nim
import json2schemalib
import std/json

let data = parseJson("""{"name":"Bob","scores":[10,20,30]}""")

# jsonToSchema — returns schema without $schema wrapper
let schema = jsonToSchema(data)

# buildSchema — adds $schema declaration at the top level
let fullSchema = buildSchema(data)

echo fullSchema.pretty()
```

### Public API

| Symbol | Description |
|--------|-------------|
| `jsonToSchema(node: JsonNode): JsonNode` | Infer schema for any JSON value |
| `mergeSchemas(schemas: seq[JsonNode]): JsonNode` | Merge per-element schemas (used for array items) |
| `inferArrayItems(arr: JsonNode): JsonNode` | Infer the `items` schema for an array |
| `buildSchema(node: JsonNode): JsonNode` | Like `jsonToSchema` but prepends `$schema` |

---

## Running tests

```bash
nimble test
```

Or directly:

```bash
nim compile -r tests/test_all.nim
```

The suite covers:

- All primitive types (`null`, `boolean`, `integer`, `number`, `string`)
- No real values leaking into the schema (strings, numbers, nested values)
- Object structure: `properties`, `required`, `additionalProperties`
- Array inference: homogeneous, mixed, nested, array-of-objects deduplication
- `mergeSchemas` edge cases: empty, single, duplicates, `anyOf`
- `buildSchema` wrapper: `$schema` presence, top-level types
- Round-trip: output is always valid JSON

---

## Design decisions

| Decision | Rationale |
|----------|-----------|
| All object keys are `required` | The sample document has all keys present; optional fields can be removed by the user |
| `additionalProperties: false` | Conservative default; encourages strict validation |
| Structural dedup for `anyOf` | Avoids redundant entries when an array has repeated shapes |
| `integer` vs `number` | Nim's `json` module distinguishes `JInt` from `JFloat`; we preserve that distinction |
| No external dependencies | Keeps the tool portable and easy to embed |

---

## License

MIT — see [LICENSE](LICENSE).
