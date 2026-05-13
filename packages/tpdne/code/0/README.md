# tpdne

Fetch and optionally save AI-generated faces from thispersondoesnotexist.com

## Installation

- Run this command in your project directory:

```bash
nimble install tpdne
```

## Usage

### Import the package

```nim
import tpdne
```

### Fetch a random face

```nim
let img = fetchImage()
```

### Optionally save it to a file

```nim
saveImage(img, "random_face.jpg")
```

## Tests

- To run tests, simply run this command:

```nim
nimble test -d:ssl
```

## Contributing

Contributions are welcome! Feel free to create an issue or open a pull request.

## Credits

Philip Wang ([@lucidrains](https://github.com/lucidrains)) - creator of [thispersondoesnotexist.com](https://thispersondoesnotexist.com)

## License

This project is licensed under the terms of the [MIT License](https://opensource.org/licenses/MIT).

SPDX-License-Identifier: MIT
