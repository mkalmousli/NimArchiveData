# Nim bindings for ALUT (OpenAL Utility Toolkit)

These are low level bindings for FreeALUT, which provides some convenience
functions for the OpenAL library.

In particular, ALUT makes it easy to correctly load WAV/RIFF/SND/AU files into
buffers as well as the useful function to create a HelloWorld buffer.

```
import alut
import openal

when isMainModule:
  if not alutInit(nil, nil):
    quit "Error during alutInit"
  var helloBuffer = alutCreateBufferHelloWorld()
  var helloSource = ALuint(0)
  alGenSources(ALsizei 1, helloSource.addr)
  alSourcei(helloSource, AL_BUFFER, Alint(helloBuffer))
  alSourcePlay(helloSource)
  discard alutSleep(1)
  if not alutExit():
    quit "Error during alutExit"
```

## TODO

Your help would be appreciated to also support Windows, MacOS/X & IOS.
