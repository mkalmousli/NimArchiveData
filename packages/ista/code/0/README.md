# Ista

Ista is a fast, minimal, terminal-based speed reader inspired by RSVP (Rapid Serial Visual Presentation). It helps you read text efficiently by displaying words (or chunks of words) at a controlled WPM, with optional focus highlighting (ORP-style).

## Features

- Blazingly Fast
- Focus/ORP Highlighting
- Chunked reading
- Command Line first
- Read text from files or stdin

## Demo
https://github.com/user-attachments/assets/609a006d-5d6b-4545-a644-47e0ea8bc4f1

## Installation
### Prerequisites
- Nim (recommended: latest stable)
- Nimble (usually packaged with nim)

### Build
```sh
nimble install https://github.com/arashi-software/ista
```

## Usage
### Basic Usage
```sh
ista myfile.txt
ista -w 300 myfile.txt
cat article.txt | ista -w 400
ista --chunk 2 -w 200 book.txt
```

### Options
| Flag | Description | Default |
|-----|------------|---------|
| `--wpm` | Target words per minute | `300` |
| `--chunk` | Number of words displayed at once | `1` |
| `--no-focus` | Disable focus highlighting | off |
| `<file>` | Input file | stdin |

#### Example
```sh
ista book.txt --wpm 500 --chunk 2 --no-focus
```

## Timing
Ista keeps true WPM accurate by:
1. Calculating the target time per word (or chunk)
2. Measuring how long terminal rendering takes
3. Sleeping only for the remaining time
This makes sure that:
```txt
(render time + sleep time) ≈ target delay
```
Even at high speeds, the effective reading rate stays consistent.

## Focus Mode (ORP)
By default, Ista highlights the Optimal Recognition Point (ORP) of each word in red. This helps your eyes fixate consistently and reduces saccades.
You can disable it with:
```sh
ista -f
```

## Roadmap
- [ ] PDF parsing
- [ ] Epub parsing
- [ ] Better text scrolling

## Housekeeping
As always, contributions, issues, and suggestions are welcome.

