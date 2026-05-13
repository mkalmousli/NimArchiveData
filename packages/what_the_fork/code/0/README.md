# What The Fork 🍴

*Sometimes it's hard to keep up to date with changes made in forks*, this tool may help.

A terminal tool that analyzes forks of a GitHub repository to extract and categorize changes like bugfixes, new features, and improvements. Now with powerful keyword search capabilities to discover what the community has been working on in repository forks!
(Note: This project is vibe-coded!)

## Features

- **Comprehensive Fork Analysis**: Analyzes all forks of a given GitHub repository
- **Smart Categorization**: Automatically categorizes commits into:
  - 🚀 **Features**: New functionality and enhancements
  - 🐛 **Bugfixes**: Bug fixes and problem resolutions
  - 💡 **Ideas**: Improvements, refactoring, and optimizations
- **🔍 Keyword Search**: Search for specific keywords across all fork commits
  - Find forks working on particular features or technologies
  - Track keyword frequency across the fork ecosystem
  - Get contextual matches with commit details
- **Activity-Based Sorting**: Prioritizes forks by star count and recent activity
- **Rate Limit Friendly**: Respects GitHub API limits with automatic delays
- **Token Support**: Optional GitHub token support for higher API rate limits
- **Flexible CLI**: Multiple argument order support for ease of use

## Installation

### Prerequisites
- Nim compiler (>= 2.2.4)

### Build from Source
```bash
git clone https://github.com/your-username/what_the_fork
cd what_the_fork
nimble build
```

The compiled binary will be available in the `bin/` directory.

## Usage

### Standard Analysis Mode
```bash
what_the_fork <github_url> [github_token]
```

### Keyword Search Mode
```bash
what_the_fork --search "keyword1,keyword2" <github_url> [github_token]
what_the_fork <github_url> [github_token] --search "keyword1,keyword2"
```

### Arguments

- **github_url**: GitHub repository URL in one of these formats:
  - `https://github.com/owner/repo`
  - `https://github.com/owner/repo.git`
  - `github.com/owner/repo`
  - `owner/repo`

- **github_token** (optional): GitHub personal access token for higher API rate limits
  - Without token: 60 requests/hour
  - With token: 5000 requests/hour
  - Get your token at: https://github.com/settings/tokens

- **--search** (optional): Comma-separated keywords for search mode
  - Case-insensitive matching
  - Searches commit messages across forks
  - Example: `--search "async,performance,memory"`

### Examples

#### Standard Analysis
```bash
# Basic usage
what_the_fork https://github.com/nim-lang/Nim

# Short format
what_the_fork nim-lang/Nim

# With GitHub token for higher rate limits
what_the_fork https://github.com/nim-lang/Nim ghp_your_token_here

# Analyze Ormin ORM forks
what_the_fork https://github.com/Araq/ormin
```

#### Keyword Search
```bash
# Search for async-related work
what_the_fork --search "async,coroutine,await" https://github.com/nim-lang/Nim

# Find performance improvements (with token)
what_the_fork https://github.com/nim-lang/Nim ghp_token --search "performance,optimize,speed"

# Search for memory management changes
what_the_fork --search "memory,gc,allocation" nim-lang/Nim

# Look for documentation improvements
what_the_fork --search "docs,readme,documentation" https://github.com/nim-lang/Nim
```

## Example Output

### Standard Analysis Mode

Here's what you'll see when analyzing the Ormin repository:

```
🔍 Analyzing forks of Araq/ormin...
📥 Fetching forks...
✅ Found 20 forks
🔬 Analyzing top 10 most active forks...

⏳ Analyzing sair770/ormin (1/10)...
================================================================================
🍴 FORK: sair770/ormin
================================================================================
⭐ Stars: 1 | 🍴 Forks: 0
📝 Description: Ormin -- An ORM for Nim. 
🔗 URL: https://github.com/sair770/ormin
📅 Last Updated: 2023-03-08T01:43:58Z
📊 Total Commits Analyzed: 73

🚀 FEATURES (10):
----------------------------------------
  • [PMunch] Implement named tuples (#12)
  • [Andreas Rumpf] first implementation of serverhttp
  • [Andreas Rumpf] fixes the protocol implementation; export all generated fields
  • [Andreas Rumpf] added support for globals in the client section
  • [Andreas Rumpf] protocol macro: added support for 'common' sections
  • [Andreas Rumpf] added support for the 'bool' type
  • [Andreas Rumpf] added support for 'case when'
  • [Andreas Rumpf] better support for verbatim SQL
  • [Andreas Rumpf] added ormin_sqlite implementation
  • [Andreas Rumpf] implemented backend specific placeholder

🐛 BUGFIXES (1):
----------------------------------------
  • [Andreas Rumpf] ormin_importer: proper error handling

================================================================================
📊 SUMMARY
================================================================================
🍴 Total Forks: 20
🔬 Analyzed: 10
🚀 Total Features Found: 45
🐛 Total Bugfixes Found: 12
💡 Total Ideas/Improvements Found: 23
📈 Total Insights: 80
```

### Search Mode Output

Here's what you'll see when searching for keywords:

```
🔍 Analyzing forks of nim-lang/Nim...
🎯 Search mode: Looking for keywords: async, performance
📥 Fetching forks...
✅ Found 156 forks
🔍 Searching for keywords: async, performance
📊 Searching across 156 forks...

================================================================================
🔍 KEYWORD SEARCH RESULTS
================================================================================
📊 Total Matches: 23

📈 KEYWORD FREQUENCY:
----------------------------------------
  🔑 'async': 15 matches
  🔑 'performance': 8 matches

🎯 MATCHES FOR 'ASYNC' (15):
--------------------------------------------------
  📍 someone/Nim
     💬 Add async support for HTTP client
     👤 John Doe | 📅 2024-01-15 | 🔗 abc1234

  📍 developer/Nim
     💬 Fix async/await memory leak issue
     👤 Jane Smith | 📅 2024-01-10 | 🔗 def5678
     🎯 Context: Fix async/await memory leak in coroutine cleanup

🎯 MATCHES FOR 'PERFORMANCE' (8):
--------------------------------------------------
  📍 optimizer/Nim
     💬 Improve performance of string operations
     👤 Bob Wilson | 📅 2024-01-12 | 🔗 ghi9012
```

## How It Works

### Standard Mode
1. **Fetches Forks**: Retrieves all forks of the specified repository using GitHub API
2. **Sorts by Activity**: Ranks forks by star count to focus on the most active ones
3. **Analyzes Commits**: Examines recent commits from each fork's default branch
4. **Categorizes Changes**: Uses pattern matching on commit messages to categorize:
   - Features: `feat:`, `add:`, `implement`, `new:`, etc.
   - Bugfixes: `fix:`, `bug:`, `resolve`, `issue`, etc.
   - Ideas: `improve:`, `refactor:`, `optimize:`, `enhance`, etc.
5. **Generates Report**: Provides a comprehensive summary of findings

### Search Mode
1. **Fetches Forks**: Retrieves all forks and sorts by activity (stars)
2. **Searches Commits**: Examines commit messages for specified keywords
3. **Extracts Context**: Finds the specific lines containing keywords
4. **Aggregates Results**: Groups matches by keyword with frequency analysis
5. **Provides Details**: Shows commit info, authors, dates, and context

## Use Cases

### Standard Analysis
- **Fork Discovery**: Find the most active and interesting forks
- **Change Tracking**: See what improvements the community is making
- **Innovation Monitoring**: Discover new features being developed

### Search Mode
- **Technology Tracking**: Find forks working with specific technologies (`--search "wasm,javascript,python"`)
- **Feature Hunting**: Look for particular features (`--search "async,parallel,concurrent"`)
- **Bug Investigation**: Track specific issues (`--search "memory,crash,segfault"`)
- **Performance Focus**: Find optimization work (`--search "performance,speed,optimize"`)

## Limitations

- **Standard mode**: Analyzes up to 10 most active forks by default
- **Search mode**: Searches up to 20 most active forks by default
- Examines up to 90 recent commits per fork
- Categorization depends on commit message patterns
- Subject to GitHub API rate limits

## Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.

## License

MIT License - see LICENSE file for details.
