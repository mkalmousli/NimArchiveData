# gbm
This library provides full Nim bindings to Mesa's [gbm](https://en.wikipedia.org/wiki/Mesa_(computer_graphics)) subsystem
alongside a work-in-progress idiomatic Nim wrapper that uses Nim's ARC/ORC move semantics alongside sanity checks to make working with gbm a hassle-free experience.

# basic example
```nim
import pkg/gbm

proc main() {.inline.} =
  var device = openDevice("/dev/dri/card1")
  echo "using gpu: " & device.path
  echo "backend: " & device.backend

  echo "allocating 1920x1080 buffer"
  var buffer = device.allocateBuffer(
    1920,
    1080,
    BufferObjectFormat.XRGB8888,
    {BufferFlags.UseScanout, BufferFlags.UseRendering},
  )
  echo "buffer format: " & $buffer.format
  echo "buffer fd: " & $buffer.getFd
  echo "plane count: " & $buffer.planeCount

  for p in 0 ..< buffer.planeCount:
    echo "plane fd: " & $buffer.getFdForPlane(p)

when isMainModule:
  main()
```

This code opens the first GPU device on your system and allocates a 1920x1080 buffer in its memory (VRAM). Notice how you don't need to manually free up the buffers afterwards like in C examples, as this library provides destructors that automatically do this as soon as the GBM buffer or device goes out of scope and is no longer needed.
