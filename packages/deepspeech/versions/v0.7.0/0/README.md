# Nim bindings for the Mozilla's DeepSpeech (v0.7.0) deep learning model.

## Prerequisites:
* Shared library ``libdeepspeech.so`` for ``v0.7.0`` which you can get after downloading native client package from their [releases](https://github.com/mozilla/DeepSpeech/releases/tag/v0.7.1) page based on your cpu architecture and operating system and extracting it.  

    _Note: Native package would  look like ``native_client.amd64.cpu.win.tar.xz`` for windows OS on a AMD64 CPU architecture_.

    _Note: This lib must be in your system's PATH or in the directory from where executable/binary is run from._

* Pretrained Models and Scorer.
    Download them from their `releases` page as well which would look  like ``deepspeech-0.7.(0|1).models.pbmm/tflite`` and ``deepspeech-0.7.(0|1).scorer``

## Note:

*   ``.wav`` files or raw audio data with ``single channel``,sampled at ``16Khz`` and having audio format as ``INT16 PCM`` are                  desired. Be sure to convert or resample data/audio according to above specifications if not already so.

## Installation
*  ``` $ nimble install deepspeech@0.7.0```  or   ``` $ nimble install https://gitlab.com/eagledot/nim-deepspeech@0.7.0```
*  ``` $ nimble install https://gitlab.com/eagledot/nim-wav ```   [optional if you need to read from or write to a ``.wav`` file , needed to run examples]

## Usage
``` nim
import deepspeech
```

For more detailed usage see examples in ``./examples/`` directory.

