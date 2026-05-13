# ED2Ksum

This is a Nim implementation of ed2ksum.
It's mostly useful for identifying large media files for use with online
databases, such as [anidb](https://anidb.net).

It uses a pure-Nim MD4 library under the hood.
The output is an array of 16 bytes.
It operates on blocks of 9728000 bytes.
Unlike most hash algorithms, ED2K blocks can be hashed in parallel, which
is nice when processing large media files.

This can be installed as a library and as a command-line tool.  The command-
line tool works a lot like `md5sum`, `sha256sum`, etc.  Give it some filenames
and it'll hash the heck outta 'em.

# Flavors

There are two flavors of algorithm, depending on a subtle difference in
behavior when the input file is an exact multiple of the block size.
This library supports both flavors, defaulting to the "blue" flavor.
See [this anidb wiki page](https://wiki.anidb.net/Ed2k-hash) for details.

The default can be changed using `NewED2K(fn, flavor=ed2kRed)`.

# Disclaimers

* As far as I know, the ED2K hash was never intended to be a secure cryptographic hash, it's meant to identify media files.
* It's based on MD4, which is kinda old, and is also not considered a secure cryptographic hash nowadays.
* The unpredictability of its output should not be relied upon for security purposes.
* This library makes no attempt to protect your data against side-channel attacks, or anything else, really.

# Example

```nim
import ed2ksum

# hash a file
let fn = "test.bin"
var ctx: ED2K_CTX
ctx = NewED2K(fn)
ctx.Run_threaded()
let rawoutput = ctx.Final()
let hexoutput = toLowerAscii(map(rawoutput, proc(x: byte): string = toHex(x)).join)
echo hexoutput
```

# License

MIT.  Have fun!
