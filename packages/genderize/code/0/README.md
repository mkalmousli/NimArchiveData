# genderize

Nim wrapper for the [Genderize.io](https://genderize.io) API

## Installation

```bash
nimble install genderize
```

## Usage

### Import the package

```nim
import genderize
```

### Initialize a client

```nim
let client = newGenderizeClient("OPTIONAL_API_KEY")
```

### Predict the gender of a single name

```nim
let genderResult = client.predictGender("Nemuel")
if isOk(genderResult):
  echo genderResult.value.gender
else: echo genderResult.error
```

### Predict the genders of multiple names

```nim
let gendersResult = client.predictGenders(@["Nemuel", "Kira"])
if isOk(gendersResult):
  for result in gendersResult.value:
    echo result.gender
else: echo gendersResult.error
```

> Both the `predictGender` and `predictGenders` methods have an optional second parameter (a 2-letter country
> ID e.g. `KE`)

## Contributing

Contributions are welcome! Feel free to create an issue or open a pull request.

## License

This project is licensed under the terms of the [GNU GPL v3.0 License](https://www.gnu.org/licenses/gpl-3.0.html).
