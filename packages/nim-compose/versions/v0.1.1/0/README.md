# Nim-compose

Composition operators for Nim.

## Usage

```nim
import compose

proc add20(x: int): int = x + 20
proc mul3(x: int): int = x * 3

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

# Make sure that the return type matches the parameter type
proc add50(x: float): float = x + 50.0
proc gt200(x: float): bool = x > 200.0

let gt150 = add50 >> gt200

140.gt150.echo
160.gt150.echo
```

## To-do
- [ ] Support overloaded procs
