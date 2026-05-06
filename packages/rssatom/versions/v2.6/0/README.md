About
=====

rssatom is a Nim module for working with RSS and [Atom](https://datatracker.ietf.org/doc/html/rfc4287)
It is based on Adam Chesak's original [nim-rss](https://github.com/achesak/nim-rss)

Installation
============

Add the following to your `{project}.nimble` file:

    requires "rssatom >= 2.0"

Usage
=====

`getRSS(url: string): RSS`
fetch and parse an RSS feed from a URL

`loadRSS(filename: string): RSS`
read and parse a local RSS file

`parseRSS(data: string): RSS`
parse RSS directly from a string

`loadAtom(filename: string): RSS`
read and parse a local Atom file

`parseAtom(data: string): RSS`
parses Atom directly from a string. See [RFC 4287](https://datatracker.ietf.org/doc/html/rfc4287)

License
=======

As its original repository, rssatom is released under the MIT Open Source License.


New Stuff coming next
=========
* a `getAtom(url: string): RSS` will be added
* a new feature will be included to create Atom from csv or json files.