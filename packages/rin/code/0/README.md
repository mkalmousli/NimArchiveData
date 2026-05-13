# rin

A tiny CLI timer. Set a timer, get a desktop notification and alarm sound.

```
$ rin 5
timer set for 5m (pid 12345)
```

The process forks to the background and prints the PID so you can `kill` it if you change your mind.

Custom messages:

```
$ rin 10 pizza is ready
timer set for 10m (pid 12346)
```

## How it works

- Alarm sound is baked into the binary (no external files needed)
- Notifications via D-Bus (`org.freedesktop.Notifications`)
- Audio playback via PulseAudio simple API

Linux only. Requires PulseAudio (or PipeWire with pulse compatibility) and a notification daemon.

## Install

```
nimble install rin
```

Or build from source:

```
nimble build
cp rin ~/bin/
```

## Build dependencies

- Nim >= 2.0
- `libpulse` (PulseAudio client library)
- `dbus-1` headers (`/usr/include/dbus-1.0`)

On Arch: `pacman -S libpulse dbus`
On Debian/Ubuntu: `apt install libpulse-dev libdbus-1-dev`
