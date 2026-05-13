# morsecode

Encode and decode text using standard international Morse code

## Setup

- Run this command in your project directory:

```bash
nimble install morsecode
```

## Usage

### Import the package

```nim
import morsecode
```

### Encoding

```nim
# output: .... . .-.. .-.. ---   .-- --- .-. .-.. -..
echo encode("HELLO WORLD)
```

### Decoding

```nim
# output: HELLO WORLD
echo decode(".... . .-.. .-.. ---   .-- --- .-. .-.. -..")
```

## Tests

- To run tests, simply run this command:

```nim
nimble test
```
