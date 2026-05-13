# nim-`crypt_blowfish`

Wrapper over Solar Designer's [`crypt_blowfish`](https://github.com/openwall/crypt_blowfish), created as a drop-in replacement for [bcryptnim](https://github.com/runvnc/bcryptnim) with better Windows compatability.

## Disclaimer from the original `crypt_blowfish.c`

```c
/*
 * The crypt_blowfish homepage is:
 *
 *	http://www.openwall.com/crypt/
 *
 * This code comes from John the Ripper password cracker, with reentrant
 * and crypt(3) interfaces added, but optimizations specific to password
 * cracking removed.
 *
 * Written by Solar Designer <solar at openwall.com> in 1998-2014.
 * No copyright is claimed, and the software is hereby placed in the public
 * domain.  In case this attempt to disclaim copyright and place the software
 * in the public domain is deemed null and void, then the software is
 * Copyright (c) 1998-2014 Solar Designer and it is hereby released to the
 * general public under the following terms:
 *
 * Redistribution and use in source and binary forms, with or without
 * modification, are permitted.
 *
 * There's ABSOLUTELY NO WARRANTY, express or implied.
 *
 * It is my intent that you should be able to use this on your system,
 * as part of a software package, or anywhere else to improve security,
 * ensure compatibility, or for any other purpose.  I would appreciate
 * it if you give credit where it is due and keep your modifications in
 * the public domain as well, but I don't require that in order to let
 * you place this code and any modifications you make under a license
 * of your choice.
 *
 * This implementation is fully compatible with OpenBSD's bcrypt.c for prefix
 * "$2b$", originally by Niels Provos <provos at citi.umich.edu>, and it uses
 * some of his ideas.  The password hashing algorithm was designed by David
 * Mazieres <dm at lcs.mit.edu>.  For information on the level of
 * compatibility for bcrypt hash prefixes other than "$2b$", please refer to
 * the comments in BF_set_key() below and to the included crypt(3) man page.
 *
 * There's a paper on the algorithm that explains its design decisions:
 *
 *	http://www.usenix.org/events/usenix99/provos.html
 *
 * Some of the tricks in BF_ROUND might be inspired by Eric Young's
 * Blowfish library (I can't be sure if I would think of something if I
 * hadn't seen his code).
*/
```

This wrapper is licensed the exact same way.

## Comparison to other bcrypt implementations

1. [bcryptnim](https://github.com/runvnc/bcryptnim)
 
```sh
user@debian:/$ ./gensalt
Rounds: 12
Salt: $2a$12$HnSiFpN3TDANiy4svxtc9.
String Length: 29
```

2. beecrypt (this project)

```sh
user@debian:/$ ./gensalt
Rounds: 12
Salt: $2a$12$mKOoZ.FyWXhb9KlEZ2l2yO
String Length: 29
```

3. [python3-bcrypt](https://github.com/pyca/bcrypt)

```sh
user@debian:/$ python3 -c "import bcrypt; print(f\"Salt: {bcrypt.gensalt().decode('utf-8')}\"); print(f\"String Length: {len(bcrypt.gensalt().decode('utf-8'))}\")"
Salt: $2b$12$9Wh3NAVkaFSTpTVBRnqtnO
String Length: 29
```
