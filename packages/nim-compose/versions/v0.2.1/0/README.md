# Nim-compose

Composition operators for Nim.

## Usage

```nim
import compose

func add20(x: int): int = x + 20
func mul3(x: int): int = x * 3

let
  add40 = add20 << add20
  mul9 = mul3 << mul3
  add80 = compose(add40, add20, add20)
  add20_then_mul3 = mul3 << add20
  mul3_then_add20 = mul3 >> add20

10.add40.echo
10.mul9.echo
10.add80.echo

10.add20_then_mul3.echo
10.mul3_then_add20.echo

func multiAdd30(x,y,z: float): float = x + y + z + 30.0
func div2(x: float): float = x / 2.0

# Multiple argument procs must be on the inner operand
let sum30over2 = multiAdd30 >> div2
sum30over2(3,4,5)

# Make sure that the return type matches the parameter type
func add50(x: float): float = x + 50.0
func gt200(x: float): bool = x > 200.0

let gt150 = add50 >> gt200

140.gt150.echo
160.gt150.echo
```

## To-do
- [ ] Support overloaded procs
- [X] Support default parameter values
- [ ] Some mechanism for allowing multi-argument procs in outer operand. Maybe currying, maybe with placeholder
