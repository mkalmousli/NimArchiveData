# NimCoon

Play videos from YouTube and PeerTube from the
command line using your preferred desktop media player.

This application is implemented in [Nim language](https://nim-lang.org) using
only the standard library.

![nimcoon screenshot with search term 'baby yoda'](screenshot.png)

## Features

- [x] Search for videos using keywords
- [x] Stream videos and music from YouTube
- [x] Play direct links from YouTube and PeerTube
- [x] Stream video and music from magnet links
- [x] Download music
- [x] Download video
- [x] Play playlists (MPV only)
- [ ] Download playlists
- [ ] Autoplay next video/audio
- [ ] Configuration options

## Installation

Nim Coon depends on the following:
- youtube-dl
- mpv (recommended) or vlc
- peerflix (for magnet links)

Install VLC or MPV using your distribution's package manager.

Install YouTube-dl
``` sh
pip3 install --user youtube-dl
```

Install PeerFlix
```sh
npm install --global peerflix
```

### Installing using Nimble

NimCoon can be installed from Nimble repositories:

``` sh
nimble install nimcoon
```

You can also install from source by running the following command:

```sh
nimble install
```

### Installing binary

Download the latest build from GitlabCI (amd64 GNU/Linux only).

```sh
wget https://gitlab.com/njoseph/nimcoon/-/jobs/artifacts/master/download?job=compile -O artifacts.zip
unzip artifacts.zip
```

Copy the binary to somewhere on your path like /usr/local/bin

## Usage

```sh
nimcoon "emacs"

# If your search query has multiple words, use quotes
nimcoon "nim lang"

# Play audio of the first search result
nimcoon -m -l "counting stars"

# Download audio of the first search result
nimcoon -mld "counting stars"

# Play direct video link
nimcoon https://www.youtube.com/watch?v=QOEMv0S8AcA

# Add -d to download or -m to select only audio or both
nimcoon -md https://www.youtube.com/watch?v=hT_nvWreIhg
```

After the search results are displayed, you can enter a number to play one
result, "all" to play all the results or "q" to quit the program. 

If a number is entered, after the selected search result is played, the results
are redisplayed, so that you can play the other results without having to search
again.

### Command line arguments

| **Arguments**     | **Explanation**                            |
|-------------------|--------------------------------------------|
| -m, --music       | Play Music only, no video                  |
| -l, --lucky       | Try your luck with the first search result |
| -f, --full-screen | Play video in full screen                  |
| -d, --download    | Download video or music                    |

Feel free to use these options in any combination. NimCoon will show a helpful
error message if you pick incompatible options.

## Development

One-liner for compiling and running

```sh
nim c -d:ssl -r src/nimcoon.nim 'nim lang'
```

## Privacy

To avoid storing your nimcoon searches in `zsh` history, run this command

```sh
setopt histignorespace
```

Then, add a space before typing nimcoon in the shell, like " nimcoon"

```sh
 nimcoon "this is private"
```

The same can be achieved in `bash` by setting an environment variable
```sh
export HISTCONTROL=ignoreboth
```
