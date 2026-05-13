# Args

A very tiny library which provides the functions `argv` and `argc` for working with command line arguments in Nim, with support for multiple Nim versions.

## Install

```shell
$ nimble add args
```

## Example

```nim
import args

when isMainModule:
  echo "total argument count: ", argc
  echo "script name: ", argv[0]

  if argc > 1:
    echo "script args: ", argv[1..^1]
```
## Development

To run tests:

```shell
$ nimble test
```

## License

Args is open source software licensed under the MIT License.
