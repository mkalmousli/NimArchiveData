# bunnery

Helpers for Nim JS builds with Bun bundling/tree-shaking.

## Install

Use Atlas for dependencies:

```bash
atlas install
```

## Preferred API (build module)

Import build procs directly:

```nim
import bunnery/build
```

Preferred high-level procs:
- `buildNimAndBundleJs` for Nim source -> Nim JS -> Bun bundle
- `combinePrebuiltNimJs` for prebuilt Nim JS + app JS -> Bun bundle

Example:

```nim
import bunnery/build

let cmds = buildNimAndBundleJs(
  nimEntry = "src/main.nim",
  cssEntries = @["src/styles.css"],
  run = false
)
echo cmds.nimCmd
echo cmds.bundleCmd
```

### Optional outputs and defaults

- `nimJsOut = ""` means Nim uses its default JS output location (`<nimEntry>.js`).
- `bundleOut = ""` means Bunnery writes to `<entry>.bundle.js`.
  - For `buildNimAndBundleJs`, this is based on resolved `nimJsOut`.
  - For `combinePrebuiltNimJs`, this is based on `prebuiltNimJs`.

### CSS example

Bundle JS with one or more CSS files:

```nim
import bunnery/build

discard buildNimAndBundleJs(
  nimEntry = "src/main.nim",
  cssEntries = @["src/styles.css", "src/theme.css"],
  bundleOut = "dist/app.bundle.js",
  run = true
)
```

Bundle a prebuilt JS entry with CSS directly:

```nim
import bunnery/build

discard bundleJs(
  entryJs = "src/app.js",
  cssEntries = @["src/styles.css"],
  bundleOut = "dist/site.bundle.js",
  run = true
)
```

## Using `buildNimAndBundleJs` in a task

Use this when you want your own task in `config.nims` or `.nimble`.

`config.nims` example:

```nim
import bunnery/build

task buildWeb, "Compile Nim to JS, then bundle with Bun":
  discard buildNimAndBundleJs(
    nimEntry = "src/main.nim",
    nimFlags = "-d:release",
    cssEntries = @["src/styles.css"],
    run = true
  )
```

`.nimble` example:

```nim
import bunnery/build

task buildWeb, "Build browser bundle":
  discard buildNimAndBundleJs(
    nimEntry = "src/main.nim",
    nimJsOut = "",
    bundleOut = "",
    cssEntries = @["src/styles.css", "src/theme.css"],
    bunFlags = "--sourcemap",
    run = true
  )
```

`combinePrebuiltNimJs` example (`config.nims`):

```nim
import bunnery/build

task bundleCombined, "Compile Nim JS with nim js, then combine with app JS":
  let prebuiltNimJs = "build/prebuilt/main.js"

  exec("nim js -d:release --out:" & prebuiltNimJs & " src/main.nim")

  discard combinePrebuiltNimJs(
    prebuiltNimJs = prebuiltNimJs,
    appJsEntry = "src/app.js",
    cssEntries = @["src/styles.css"],
    bundleOut = "dist/app.bundle.js",
    run = true
  )
```

## Tasks module (imported separately)

Task definitions are in `bunnery/tasks` and should be included from NimScript files (`config.nims` or `.nimble`):

```nim
include bunnery/tasks
```

This adds:
- `bunBuild`
- `bunCombine`
- `bunDownload`

Env vars supported by tasks:
- `BUNNERY_NIM_ENTRY`
- `BUNNERY_NIM_JS_OUT`
- `BUNNERY_BUNDLE_OUT`
- `BUNNERY_CSS_ENTRIES` (comma-separated, e.g. `src/styles.css,src/theme.css`)
- `BUNNERY_NIM_FLAGS`
- `BUNNERY_BUN_FLAGS`
- `BUNNERY_PREBUILT_NIM_JS`
- `BUNNERY_APP_JS_ENTRY`
- `BUN_VERSION` (optional, for `bunDownload`)

## Include helper sample

Runnable NimScript sample:

```bash
nim e --path:src tests/samples/include_bunnery.nims
```
