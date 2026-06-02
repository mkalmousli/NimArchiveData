# Nim interface to WEBRTC Voice Activity Detector (VAD).
Actual code for VAD is developed by the Google for the [WebRTC](https://webrtc.org) project and is one the best freely available VAD engine.


## Installation
* ```$ nimble install https://gitlab.com/eagledot/nim-webrtcvad```
    ### OR
* ```$ nimble install webrtcvad```

## Usage:
1.Create and Initialize a VAD object
```nim
import webrtcvad
var vad: vadObj
var code = initVad(vad)
```
2.Set its aggressiveness mode, which is an integer between 0 and 3. 0 is the least aggressive about filtering out non-speech, 3 is the most aggressive. 
```nim
discard setMode(vad,3'i32)  #can use return value to check if mode set successfully
```
3.Test it on a short segment  of audio. The WebRTC VAD only accepts 16-bit mono PCM audio, sampled at 8000, 16000, 32000 or 48000 Hz. A frame must be either 10, 20, or 30 ms in duration:
```nim
let sampleRate = 16000*2
let frameDuration = 30 #in ms
#for this fakeData should return 0 
let fakeData = newSeq[int16](int((frameDuration*sampleRate)/1000))
#returns 1 if voice activity detected else 0 but returns -1 when some error occured.
code = isSpeech(vad,fakeData,sampleRate)
```

