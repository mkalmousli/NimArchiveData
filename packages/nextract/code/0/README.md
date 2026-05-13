# nextract

[![CI Status](https://github.com/nim-community/nextract/workflows/CI/badge.svg)](https://github.com/nim-community/nextract/actions)
[![Nim Version](https://img.shields.io/badge/nim-1.6.18%2B-blue.svg)](https://nim-lang.org)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A content extraction library for Nim.

This library extracts article content from HTML web pages, inspired by Mozilla Readability. It uses [chame](https://git.sr.ht/~bptato/chame) for HTML5 parsing and [chagashi](https://git.sr.ht/~bptato/chagashi) for character encoding support.

## Features

- Extract main article content from HTML pages
- Remove navigation, ads, sidebars, and other clutter
- Extract metadata (title, author, excerpt, site name, etc.)
- Convert relative URLs to absolute
- Configurable extraction options
- `isProbablyReaderable` quick check

## Installation

```nim
# In your .nimble file
requires "nextract"
```

Or install directly:

```bash
nimble install nextract
```

## Dependencies

- `nim >= 1.6.18` - Nim compiler
- `chame >= 1.0.0` - HTML5 parser
- `chagashi >= 0.7.0` - Character encoding support

### Nim Version Compatibility

| Nim Version | Status |
|-------------|--------|
| 1.6.18+ | ✅ Supported |
| 2.0.x | ✅ Supported |
| 2.2.x | ✅ Supported |
| devel | ✅ Supported |

## Usage

### Basic Usage

```nim
import nextract

let html = readFile("article.html")
let extractor = newExtractor(html, "https://example.com")
let article = extractor.parse()

if article.isSome:
  echo "Title: ", article.get.title
  echo "Author: ", article.get.byline
  echo "Content: ", article.get.content
  echo "Text: ", article.get.textContent
  echo "Length: ", article.get.length
```

### Quick Check

```nim
import nextract

let extractor = newExtractor(html)
if extractor.isProbablyReaderable():
  let article = extractor.parse()
  # Process article...
```

### Custom Options

```nim
import nextract

let options = initOptions(
  charThreshold = 500,        # Minimum characters for valid article
  keepClasses = false,        # Preserve CSS classes
  classesToPreserve = @[]     # Classes to preserve when cleaning
)

let extractor = newExtractor(html, "https://example.com", options)
let article = extractor.parse()
```

## API Reference

### `newExtractor(html, baseUri, options)`

Creates a new content extractor instance.

- `html: string` - The HTML content to parse
- `baseUri: string` - Base URL for resolving relative links
- `options: ExtractOptions` - Configuration options

### `parse()`

Parses the document and extracts article content.

Returns `Option[Article]` containing:
- `title: string` - Article title
- `content: string` - Clean HTML content
- `textContent: string` - Plain text content
- `length: int` - Character count
- `excerpt: string` - Short description
- `byline: string` - Author information
- `dir: string` - Text direction (ltr/rtl)
- `siteName: string` - Site name
- `lang: string` - Content language
- `publishedTime: Option[DateTime]` - Publication time

### `isProbablyReaderable(minContentLength, minScore)`

Quick check if document is likely to contain readable content.

- `minContentLength: int = 140` - Minimum node content length
- `minScore: int = 20` - Minimum cumulated score

### `initOptions(...)`

Creates configuration options:

- `charThreshold: int = 500` - Minimum characters for article
- `classesToPreserve: seq[string] = @[]` - Classes to keep
- `keepClasses: bool = false` - Preserve all classes

## Algorithm

The library implements a content extraction algorithm inspired by Mozilla Readability:

1. **Preprocessing** - Remove scripts, styles, and normalize the DOM
2. **Scoring** - Score paragraphs and containers based on:
   - Text length and punctuation (commas indicate sentences)
   - Class/id names (positive: article, content, entry; negative: sidebar, footer, ad)
   - Link density (high density = navigation)
3. **Candidate Selection** - Pick the container with highest score
4. **Cleanup** - Remove remaining clutter and fix URLs

## License

MIT License

## Credits

- Mozilla Readability - Inspiration and algorithm reference
- chame - HTML5 parser by ~bptato
- chagashi - Character encoding support by ~bptato
