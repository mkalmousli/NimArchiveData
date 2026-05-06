About
=====

rssatom is a Nim module for working with RSS and [Atom](https://datatracker.ietf.org/doc/html/rfc4287)
It is based on Adam Chesak's original [nim-rss](https://github.com/achesak/nim-rss)


Installation
======

You can either install this by downloading the git and cd'ing and typing

```
nimble install
```

or you can try:
```
nimble install https://codeberg.org/samsamros/rssatom.git

```

or, as of January 2024:
```
nimble install rssatom
```


You can also run the tests inside to see if the package is working properly 
```
nimble test
```


Usage
=====

* A real case can bee seen here: [rss creator](https://codeberg.org/samsamros/rss-creator)

`getRSS(url: string): RSS`
fetch and parse an RSS feed from a URL

`loadRSS(filename: string): RSS`
read and parse a local RSS file

`parseRSS(data: string): RSS`
parse RSS directly from an appropriate XML string for RSS version 2.0

`loadAtom(filename: string): RSS`
read and parse a local Atom file

`parseAtom(data: string): RSS`
parses Atom directly from an appropriate XML string. See [RFC 4287](https://datatracker.ietf.org/doc/html/rfc4287)

the `RSS` object is the same for both Atom and RSS. When the RSS object is transformed to XML the correct proc must be used. 

#### parsing RSS

```nim
#from tests

let 
  feed = loadRSS("./tests/democracynow.rss")
echo(feed.title.get())
echo(feed.link.get())
echo(feed.docs.get())
echo(feed.items.len)
echo(feed.items[0].title.get())

```

The items are obtained by traversing through the XML's structure. the items is a `seq[RSSItem]` which can be traversed by iteration.

#### converting back to XML

```nim
#from tests

let 
  feed = loadAtom("./tests/proceso.xml")
  parsedXml = buildAtom(feed)
  reloadfeed = parseAtom($parsedXml)

echo(feed.id.get() == reloadfeed.id.get())
echo(feed.author.name.isNone())
echo(reloadfeed.author.name.get == "")
echo(feed.items[0].title.get == reloadfeed.items[0].title.get)

#Dump file

dumpAtom("./tests/redoingProceso.xml", feed)

#crossdump

dumpRss("./tests/redoingProceso.rss", feed)
```

**warning**: cross dumping does not work perfectly, because of how RSS and Atom handle links, authors, updates, etc. See [RFC 4287](https://datatracker.ietf.org/doc/html/rfc4287) for more information. The example above is only meant to be illustrative, and not produce actual reliable results when it comes to cross-dumping.

#### parsing Atom

```nim
let 
  feed = loadAtom("./tests/headlines.atom")
echo(feed.title.get())# "The Register"
echo(feed.link.get() # "https://www.theregister.com/headlines.atom"
echo(feed.description.isSome())
echo(feed.copyright.get()) # "Copyright © 2023, Situation Publishing"
```

As stated before, the RSS object remains the same, users are encouraged to see how these translate to XML nodes. This can be done by looking at the `buildRss` and `buildAtom` procs, and by looking at [RFC 4287](https://datatracker.ietf.org/doc/html/rfc4287).

```nim
import xmltree, rssatom, options


var rss = RSS()

rss.title = "This is a test".some()
rss.link = "link.com".some()
rss.description = "this is a test for this and that".some()


let atomTest = buildAtom(rss)
let rssTest = buildRss(rss)

echo($atomTest)
echo($rssTest)
```
----

```xml
<feed xml:lang="" xmlns="https://datatracker.ietf.org/doc/html/rfc4287">
  <id></id>
  <title>This is a test</title>
  <subtitle type="html">this is a test for this and that</subtitle>
  <link type="application/atom+xml" rel="self" href="link.com" />
  <rights></rights>
  <author>
    <name></name>
    <email></email>
    <uri></uri>
  </author>
  <updated></updated>
</feed>

<rss version="2.0">
  <channel>
    <title>This is a test</title>
    <description>this is a test for this and that</description>
    <link>link.com</link>
    <copyright></copyright>
    <author></author>
    <lastBuildDate></lastBuildDate>
  </channel>
</rss>

```

#### links and Author

links and author are handled differently in Atom and RSS. links are included as an attribute in Atom and as text in RSS. Users should be mindful of this and also regarding Authors. To avoid more problems RSS objects that deal with RSS only use *name* in the Author object, whereas Atom also uses *email* and *uri* if available. This schema is further explained in [RFC 4287](https://datatracker.ietf.org/doc/html/rfc4287).

```nim
import xmltree, rssatom, options


var rss = RSS()
var atom = RSS()

rss.title = "This is a test".some()
rss.link = "link.com".some()
rss.author.name = "Samuel R.".some()
rss.description = "this is a test for this and that".some()

atom.title = "this is atom".some()
atom.link = "link2.com".some()
atom.author = Author(name: "Samuel R.".some(), email: "myemail@email.com".some(), uri: "uri.com".some())

let atomTest = buildAtom(atom)
let rssTest = buildRss(rss)

echo($atomTest)
echo($rssTest)

```

----

```xml
<feed xml:lang="" xmlns="https://datatracker.ietf.org/doc/html/rfc4287">
  <id></id>
  <title>this is atom</title>
  <subtitle type="html"></subtitle>
  <link type="application/atom+xml" rel="self" href="link2.com" />
  <rights></rights>
  <author>
    <name>Samuel R.</name>
    <email>myemail@email.com</email>
    <uri>uri.com</uri>
  </author>
  <updated></updated>
</feed>

<rss version="2.0">
  <channel>
    <title>This is a test</title>
    <description>this is a test for this and that</description>
    <link>link.com</link>
    <copyright></copyright>
    <author>Samuel R.</author>
    <lastBuildDate></lastBuildDate>
  </channel>
</rss>

```

License
=======

As its original repository, rssatom is released under the MIT Open Source License.


This is under development
========

This project is under development, and will include new features in the future. As most software, this is done on my spare time. I love to contribute, but I may not have a lot of time to handle issues on time.