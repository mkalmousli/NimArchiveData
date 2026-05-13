# html
Typed HTML5 element data and builder for structured HTML

### Features

- ✅ A complete, typed table of all official HTML5 elements and their attributes, including `deprecated` and `experimental` flags.
- ✅ `HTMLNode`/`HTML` types for constructing HTML trees in code.
- ✅ Utilities for rendering, walking, comparing, and inspecting HTML data.
- ✅ Helpers for querying HTML5 metadata like `getElement`, `isElement`, `isDeprecated`, `isAttribute`, `isExperimental`.

### Installation
```bash
nimble install html
```

## Builder Example
```nim
import html

let htmlTree: HTML = @[
  HTMLNode(kind: htmlnkElement, tag: "section", attributes: {"id": "main"}.toTable,
    children: @[
      HTMLNode(kind: htmlnkElement, tag: "h1", children: @[HTMLNode(kind: htmlnkText, text: "Welcome")]),
      HTMLNode(kind: htmlnkText, text: "\n"),
      HTMLNode(kind: htmlnkElement, tag: "p", children: @[HTMLNode(kind: htmlnkText, text: "This is Nim-generated HTML.")])
    ])
]

echo $htmlTree           # HTML string
echo treeRepr(htmlTree)  # Tree representation
```
```
<section id="main"><h1>Welcome</h1>
<p>This is Nim-generated HTML.</p></section>

[section]: id="main" 
    [h1]: 
        [Text]: Welcome
    [/h1]
    [Text]: 

    [p]: 
        [Text]: This is Nim-generated HTML.
    [/p]
[/section]
```

## Metadata Example
```nim
import html

echo isElement("div")                           # true
echo isAttribute("a", "href")                   # true
echo isDeprecated("applet")                     # true
echo isExperimental("dialog")                   # false
echo isDeprecated("a", "ping")                  # false

echo getElement("a").getAttributes()            # {"shape": (deprecated: true, experimental: false), "charset": (deprecated: true, experimental: false), "ping": (deprecated: false, experimental: false), "rev": (deprecated: true, experimental: false), "referrerpolicy": (deprecated: false, experimental: false), "attributionsrc": (deprecated: false, experimental: true), "href": (deprecated: false, experimental: false), "name": (deprecated: true, experimental: false), "download": (deprecated: false, experimental: false), "type": (deprecated: false, experimental: false), "target": (deprecated: false, experimental: false), "coords": (deprecated: true, experimental: false), "hreflang": (deprecated: false, experimental: false), "rel": (deprecated: false, experimental: false)}
```