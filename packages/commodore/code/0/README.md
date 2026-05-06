Commodore
==========

Commodore is a simple to use library for parsing command line arguments. 
Commodore is ideal for writing terminal applications. All variables are
defined right way, and are immediately accessable to your program. Some of 
the highlight features also include:

- subcommands
- options 
- flags
- exit options (for custom help and version messages)
- custom error messages
- arguments

This library is a fork of Casey McMahon's [Commandant](https://github.com/casey-SK/commandant).

Usage
-----

<br/>

**example1.nim**
```nim
import commodore, strformat

commandline:
  argument(value, int) # REQUIRED Argument
  argument(value2, int, false) # OPTIONAL Argument[REQUIRED=false]
  flag(selfDestruct, @["destroy", "d"])
  option(timer, float, @["time", "t"], 12.0) # last parameter is a default value
  
  # if you define an argument, they are REQUIRED by default.
  # commodore will terminate the program if an argument is missing (for REQUIRED).
  # conversely, flags are optional and the program will run whether they are 
  #     provided or not.
  # options are also optional

if selfDestruct:
  echo fmt"Using {value} volts, the computer will go to sleep in {timer} minutes"  
else:
  echo fmt"I will continue to perform my duties, running at {value} Hz"
```
terminal:
```bash
./example1 -d -t:5.2 1000
```
output:
```
Using 1000 volts, the computer will go to sleep in 5.2 minutes
```

<br/>

**example2.nim**
```nim
import ../commodore, strformat

const myProgramVersion = "0.5.4"

proc helpMessage(): string =
  result = """
  pico-nim project template builder
  Usage: example2 <subcommand> <subcommand options> <sillyArg>
  Available subcommands: 
    {...}
  """

commandline:
  argument(sillyArg, float) 
  exitoption(@["help", "h"], helpMessage())
  exitoption(@["version", "v"], myProgramVersion)
  errormsg("you have made some kind of mistake")
  subcommand init, @["init", "i"]:
    argument(name, string)
    flag(override, @["override", "O"])
    option(sdk, string, @["sdk", "s"])
    option(nimbase, string, @["nimbase", "n"])
    exitoption(@["help", "h"], "I wish i could help you, but I can't")
  subcommand build, @["build", "b"]:
    argument(mainProgram, string)
    option(output, string,@["output", "o"], "source/build")
  
if init:
  echo fmt"creating a new project directory, {name}. One second..."
elif build:
  echo fmt"writing {name}.uf2 to {output}..."
else:
  echo "please use either the 'init' or 'build' subcommand..."
  echo helpMessage()
```


Installation
------------

There are 2 ways to install commodore:

**nimble**

Install [nimble](https://github.com/nim-lang/nimble). Then do:

    $ nimble install commodore

This will install the latest tagged version of commodore.

**raw**

Copy the commodore.nim file to your project and import it.

When I go this way for Nim libraries, I like to create a `libs/`
folder in my project and put third-party files in it. I then add the
line `path = "libs"` to my `nim.cfg` file so that the `libs/`
directory is looked into at compile time.


Documentation
-------------

**commandline**

`commandline` is used to delimit the space where you define the command line
arguments and options you expect. All other commodore constructs (described below)
are placed under it. They are all optional - although you probably want to use
at least one, right?

**subcommand**(`identifier`, `name`, @[ `alias1`, `alias2`...])

`subcommand` declares `identifier` to be a variable of type `bool` that is `true`
if the first command line argument passed is `name` or one of the aliases (`alias1`, `alias2`, etc.) and is `false` otherwise.
Under it, you define the subcommand arguments and options you expect.
All other commodore constructs (described below) *can be* placed under it.

<br/>

**argument**(`identifier`, `type`, `required`)

`argument` declares a variable named `identifier` of type `type` initialized with
the value of the corresponding command line argument converted to type `type`.
`required` is a bool used to switch an argument to be optional or a necessity

Correspondence works as follows: the first occurrence of `argument` corresponds
to the first argument, the second to the second argument and so on. Note that
if a `subcommand` is declared then 1) any top-level occurrence of `argument` is
ignored, 2) the first subcommand `argument` corresponds to the first command line argument
after the subcommand, the second to the second argument after the subcommand and so on.

<br/>

**arguments**(`identifier`, `type` , `atLeast1`)

`arguments` declares a variable named `identifier` of type `seq[type]` initialized with
the value of the sequential command line arguments that can be converted to type `type`.
By default `atLeast1` is `true` which means there must be at least one argument of type
`type` or else an error is thrown. Passing `false` there allows for 0 or more arguments of the
same type to be stored at `identifier`.

*Warning*: `arguments myListOfStrings, string` will eat all arguments on
the command line. The same applies to other situations where one type is
a supertype of another type in terms of conversion e.g., floats eat ints.

<br/>

**flag**(`identifier`, @[ `alias1`, `alias2`...])

functionally, flags are just options (defined below) that have a pre-defined
boolean `type` and a `default` value set to `false`.

<br/>

**option**(`identifier`, `type`, @[ `alias1`, `alias2`...] , `default`)

`option` declares a variable named `identifier` of type `type` initialized with
the value of the corresponding command line option `--alias`(if long) or `-alias`(if short)
converted to type `type` if it is present. The `--` and `-` are added
by commodore for your convenience. If the option is not present,
`identifier` is initialized to its default type value or the passed
`default` value.

The command line option syntax follows Nim's one and adds space (!) i.e.,
`--times=3`, `--times:3`, `-t=3`, `-t:3`, `--times 3` and `-t 3` are all valid.

Syntactic sugar is provided for boolean options such that only the presence of
the option is needed to give a true value.

<br/>

**exitoption**(@[ `alias1`, `alias2`...], `exit message`)

`exitoption` declares a long and short option string for which the application
will immediately output `exit message` and exit. This can be used for subcommand specific exit messages too:

This is mostly used for printing the version or the help message.

<br/>

**errormsg**(`custom error message`)

`errormsg` sets a string `custom error message` that will be displayed after the other error messages if the command line arguments or options are invalid.


<br/>


**Valid types for `type` are:**

- `int`, `float`, `string`, `bool`, `char`


<br/>


Design
------

- Keep as much logic out of the module and into the hands of
  the developer as possible
- No magical variables should be made implicitly available. All created
  variables should be explicitly chosen by the developer.
- Keep it simple and streamlined. Command line parsers can do a lot for
  you, but I prefer to be in adequate control.
- Test in context. Tests are run on the installed package because that
  is what people get.


<br/>

Tests
-----

Run the test suite:

    nimble test

<br/>

TODO and Contribution
---------------------

### Version 0.18.0 
- Add `help=` string variable to all argument types, which will store a description of the argument
- Create default `--help` command that outputs a typical help message

### Version 0.21.0
- Create an html AND markdown version of the help message

### Version 1.0.0
- Improve error handling and update tests to reflect this
