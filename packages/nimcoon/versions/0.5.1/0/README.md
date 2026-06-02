# NimCoon

Play videos from YouTube and PeerTube from the
command line using your preferred desktop media player.

NewPipe app for Android offers similar features.

This application is implemented in [Nim language](https://nim-lang.org) using
only the standard library.

![nimcoon screenshot with search term 'baby yoda'](screenshot.png)

## Motivation

### No similar existing tool

I tried all kinds of alternative YouTube players for the desktop. Most are either electron apps or slow web proxies. There was one cool bash script that did most of what I wanted called `ytview` but it had no way of copying the URL. I added this feature to the bash script but was still unsatisfied. There were CLI players written in Python but are usually a pain to install. They depend on a hard-coded YouTube API token and suffer from rate-limiting. I use youtube-dl with mpv usually (mostly because I like MPV over the YouTube JavaScript player). These tools have hundreds of options and I can't remember them. I needed a wrapper for them that does what I want, is easy to install(portable) and has a simple interface. I am not a big fan of Golang and its fat binaries. I discovered Nim around the same time I had this problem and decided to write this tool using just the standard library.

### Digital Minimalism

YouTube's business incentive is to make you watch as many videos as possible. If you open the YouTube website and are logged into it, you will just get distracted by recommendations and forget why you opened it in the first place. You might have wanted to watch a conference talk but end up going down a rabbit hole of other "interesting" videos customized for you. 

NimCoon has a spartan design. It doesn't even show images of the search results. I have had better success with managing my YouTube consumption after shifting to this tool. Pinafore's wellness settings are an inspiration for this.

### Why no issues or merge requests?

I made this just for myself. The development is completely based on my needs and wants. I am not going to put a lot of work into this project. But feel free to use it if it works for you.

## Features

- [x] Search for videos using keywords
- [x] Stream videos and music from YouTube
- [x] Play direct links from YouTube and PeerTube
- [x] Stream video and music from magnet links and hyperlinks to torrent files
- [x] Download music
- [x] Download video
- [x] Play YouTube playlists (MPV only)
- [ ] Download YouTube playlists
- [x] Stream video from torrent file URLs
- [x] BitTorrent is preferred for PeerTube video links
- [ ] Search PeerTube (3.0 or later)
- [ ] YouTube Autoplay (music only)
- [ ] Configuration options

|                         | YouTube  | PeerTube (HTTP) | PeerTube (WebTorrent) |
| --------                | -------- | --------        | --------              |
| Music Streaming         | ✅       | ✅              | ✅                    |
| Video Streaming         | ✅       | ✅              | ✅                    |
| Music Download          | ✅       | ✅              |                       |
| Video Download          | ✅       | ✅              |                       |
| Stream Music from URL   | ✅       | ✅              |                       |
| Stream Video from URL   | ✅       | ✅              | ✅                    |
| Download Music from URL | ✅       | ✅              |                       |
| Download Video from URL | ✅       | ✅              |                       |
| Stream Music Playlist   | ✅       |                 |                       |
| Stream Video Playlist   | ✅       |                 |                       |
| Download Music Playlist |          |                 |                       |
| Download Video Playlist |          |                 |                       |

## Installation

Nim Coon depends on the following:
- youtube-dl
- mpv (recommended) or vlc
- peerflix and webtorrent (for magnet links)

Install MPV or VLC using your distribution's package manager.

Install YouTube-dl
``` sh
pip3 install --user youtube-dl
```

Install PeerFlix and WebTorrent
```sh
npm install --global peerflix webtorrent-cli
```

(Optional) If you want your YouTube downloads to be faster, install `aria2` download manager.

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

