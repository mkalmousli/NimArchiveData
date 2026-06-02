# camelize

> Convert json node to camelcase

## Usage

```nim
import camelise
import json

camelize(%*{"snake_case": 1, "kebab-case": 2}) # %*{"snakeCase": 1, "kebabCase": 2}
```
