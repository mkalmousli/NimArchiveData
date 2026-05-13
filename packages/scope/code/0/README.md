# scope

Lightweight library for tracking scope and identifier usage in untyped `Nim` macros. Builds live scope stack during AST traversal, recording definitions, usages, and shadowing with rich metadata.

## Features

- **Complete scope tracking** - Procedures, blocks, loops, conditionals, exception handling
- **Variable shadowing detection** - Multi-level shadowing analysis with scope depths
- **Cross-scope access patterns** - Track variables used from outer scopes
- **Rich metadata** - Line numbers, scope depths, usage classifications
- **Custom traversal callbacks** - Hook into AST walking process
- **Persistent storage** - No data loss during scope transitions
- **Zero dependencies** - Pure Nim, works with any macro

## Quick Start

```nim
import scope

macro analyzeScope(body: untyped): untyped =
  let scope = newScope()
  scope.walk(body)
  
  # Check for variable shadowing
  for name, info in scope:
    if scope.isShadowed(name):
      echo name, " is shadowed"
  
  result = body

analyzeScope:
  var x = 1
  if true:
    var x = 2  # Detected as shadow
```

## API Reference

### Core Types

```nim
type
  UsageKind = enum
    ukDef     # Variable definition
    ukUse     # Variable usage
    ukShadow  # Shadow definition

  UsageInfo = object
    node*: NimNode
    kind*: UsageKind
    scopeDepth*: int
    line*, col*: int

  IdentInfo = object
    name*: string
    all*: seq[UsageInfo]  # All usages
    def*: UsageInfo       # Definition info
```

### Main API

```nim
# Create tracker
proc newScope*(): ScopeTracker

# Walk AST
proc walk*(st: ScopeTracker, root: NimNode)
proc walk*(st: ScopeTracker, root: NimNode, cb: WalkCallback)

# Query identifiers
proc info*(st: ScopeTracker, name: string): IdentInfo
proc info*(st: ScopeTracker, ident: NimNode): IdentInfo
proc allTracked*(st: ScopeTracker): seq[IdentInfo]

# Scope queries
proc currentScopeSymbols*(st: ScopeTracker): seq[string]
proc currentScopeLevel*(st: ScopeTracker): int
proc inCurrentScope*(st: ScopeTracker, name: string): bool
proc scopeOf*(st: ScopeTracker, name: string): int

# Analysis helpers
proc isDefined*(st: ScopeTracker, name: string): bool
proc isShadowed*(st: ScopeTracker, name: string): bool
proc usagesOf*(st: ScopeTracker, name: string): seq[UsageInfo]
proc shadowedIdents*(st: ScopeTracker): seq[IdentInfo]

# Iterators
iterator items*(st: ScopeTracker): IdentInfo
iterator pairs*(st: ScopeTracker): (string, IdentInfo)
iterator itemsInScope*(st: ScopeTracker, level: int): IdentInfo
```

## Usage Patterns

### Variable Shadowing Detection

```nim
macro checkShadowing(body: untyped): untyped =
  let scope = newScope()
  scope.walk(body)
  
  for shadowedVar in scope.shadowedIdents():
    echo "Warning: ", shadowedVar.name, " is shadowed"
  
  result = body
```

### Cross-Scope Analysis

```nim
macro findCrossScope(body: untyped): untyped =
  let scope = newScope()
  scope.walk(body)
  
  for name, info in scope:
    let usageDepths = info.all.mapIt(it.scopeDepth)
    if usageDepths.len > 1:
      echo name, " accessed across scopes: ", usageDepths
```

### Macro Hygiene

```nim
macro hygieneCheck(newVarName: static[string], body: untyped): untyped =
  let scope = newScope()
  scope.walk(body)
  
  if scope.isDefined(newVarName):
    error("Variable name conflict: " & newVarName)
  
  # Safe to generate code with newVarName
```

### Custom Callbacks

```nim
proc scopeCallback(n: NimNode, st: ScopeTracker) =
  if n.kind == nnkProcDef:
    echo "Entering procedure at depth ", st.currentScopeLevel()

scope.walk(body, scopeCallback)
```

## Supported Constructs

**Procedures**: `proc`, `func`, `method`, `converter`, `iterator`, `template`, `macro`  
**Control Flow**: `if`/`elif`/`else`, `case`/`of`, `for`, `while`, `block`  
**Exception Handling**: `try`/`except`/`finally`  
**Variable Declarations**: `var`, `let`, `const`  
**Parameters**: All procedure parameter types

## Use Cases

- **Hygiene-aware macro generation** - Avoid variable name conflicts
- **Static analysis tools** - Detect unused variables, scope violations
- **Code transformation** - Safely rename/refactor variables
- **Template engines** - Generate code with proper scoping
- **DSL implementation** - Build domain-specific languages
- **Debugging macros** - Understand variable lifetime and visibility

## Examples

See `examples/example.nim` for comprehensive demonstrations including:
- Multi-level variable shadowing
- Cross-scope variable access tracking  
- Procedure parameter analysis
- Nested scope depth visualization
- Macro hygiene checking

## Requirements

- Nim 1.6+ (uses modern macro APIs)
- No external dependencies

## License

MIT
