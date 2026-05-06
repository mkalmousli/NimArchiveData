# Yaclap

Yet another command line argument parser for Nim.

## Example

```nim
import yaclap

var app = newApp("MyApp")[]
  .version("1.0")
  .author("John Doe <example@email.com>")
  .about("Does awesome things")
  .arg(
    newArg("config")
    .short("-c")
    .long("--config")
    .help("Sets a custom config file")
    .takes_value(true)
  )
  .arg(
    newArg("output")
    .help("Sets an optional output file")
    .index(1)
  )
  .arg(
    newArg("debug")
    .short("-d")
    .multiple(true)
    .help("Turn debugging information on")
  )
  .subcommand(
    newSubCommand("test")
    .about("Has test sub functionality")
    .arg(
      newArg("quiet")
      .short("-q")
      .help("Display less verbose information")
    )
  )

var matches = app.get_matches()

if letSome(o, matches.value_of("output")):
  echo("Value for output: ", o)

if letSome(c, matches.value_of("config")):
  echo("Value for config: ", c)

case matches.occurrences_of("debug")
of 0: echo("Debug mode is off")
of 1: echo("Debug mode is kind of on")
of 2: echo("Debug mode is on")
else: echo("Don't be crazy")

if letSome(matches, matches.subcommand_matches("test")):
  if matches.is_present("quiet"):
    echo("Printing quieter test info...")
  else:
    echo("Printing verbose test info...")
```

## See also

Other Nim command-line argument parsers:

- [`std/parseopt`](https://nim-lang.org/docs/parseopt.html)
- [`cligen`](https://github.com/c-blake/cligen)
- [`argparse`](https://github.com/iffy/nim-argparse)
- [`therapist`](https://bitbucket.org/maxgrenderjones/therapist)
- [`docopt`](https://github.com/docopt/docopt.nim)
- [`commandant`](https://github.com/casey-SK/commandant)
- [`clapfn`](https://github.com/oliverdelancey/clapfn)
- [`nclap`](https://github.com/AinTEAsports/nclap)

## Goals and Design

There were already **a lot** of command-line argument parsers for Nim,
but foremost what I wanted is something as featureful and as declarative
as possible because these are the kind of modern parsers that seem to
be the most popular in other languages nowadays, like C++'s `argparse`,
Rust's `clap`, Zig's `clap`, JavaScript's `commander`, etc.

Nim's unofficial motto is "Copying bad design is not good design", and
as so I've looked at several of these parsers and decided that Rust's
`clap` was a good building block to then polish to fit Nim's style.
To that end, the rough steps for this project are:

1. Port and get as many features from Rust's `clap`'s "builder API" as we can
2. Add a simpler and more Nim-like API (maybe with named arguments + templates like Nim's `argparse`)
3. Add features that are still not in Rust's `clap`, like option prefixes
4. Simplify the API even more (maybe by parsing strings) and we'll go from there

## Licence

MIT
