# CIPHERLIB
**Cipherlib** is a library for encoding and decoding text.

# Installation
```
nimble install cipherlib
```

# Usage
Cipherlib supports more than one ciphers or encoding

# Importing the package
``` nim
import cipherlib
```

# Caesar cipher encoding and decoding
```nim
let word = "welcome"
let key = 12

#caesar encoding
let encodedWord = caesarEncode(word, key)
echo encodedWord

#caesar decoding
echo caesarDecode(encodedWord, key)
```

# ROT13 cipher encoding and decoding
```nim
let word = "welcome"

#rot13 encoding
let encodedWord = rot13(word)
echo encodedWord

#rot13 decoding
echo rot13(encodedWord)
```

# ROT47 cipher encoding and decoding
``` nim
let word = "welcome"

#rot47 encoding
let encodedWord = rot47Encode(word)
echo encodedWord

#rot47 decoding
echo rot47Decode(encodedWord)
```

# Vigenere cipher encoding and decoding
``` nim
let word = "welcome"

#vigenere cipher encoding
let encodedWord = vigenereEncode(word)
echo encodedWord

#vigenere cipher decoding
echo vigenereDecode(encodedWord)
```

# Morse encoding and decoding
```nim
let word = "welcome"

#morse encoding
let morseWord = morseEncode(word)
echo morseWord

#morse decoding
echo morseDecode(morseWord)
```

