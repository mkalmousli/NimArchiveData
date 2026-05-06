# morse
A morse encoding and decoding tool.

# Installation
``` nim
nimble install morse
```

# Usage
``` bash
morse -h
```

# Help Output
``` bash
        ===============================================================         
                 Morse 0.1.0 - Morse Code encoding and decoding                 
        ===============================================================         
 Encode or Decode file in Morse Code.

 
Usage: morse <FILE> [OPTIONS] 

 Input may be a file path or stdin

 When no FILE is provided, read from standard input

                              ===== ARGUMENT =====                              
 <FILE>                       File for morse encoding/decoding.

                              ===== OPTIONS =====                               
 -d, --decode                 Decode morse code
 -h, --help                   Show this help.
 -v, --version                Show version information.


                             ====== EXAMPLES =====                              
  # Encoding
    morse my_file.txt

  # Text Encoding
    echo 'hello world' | morse

  # Decoding
    morse enc_file.txt -d

  # Text Decoding
    echo '.... . .-.. .-.. ---   .-- --- .-. .-.. -..' | morse -d

 NOTE: input containing spaces or special characters should be placed in quotes.



```

**FUN FACT**: The first Morse code message was "What hath God wrought?" transmitted by Samuel Morse on May 24, 1844. 
Today it's mostly used in CTF challenges

# TAKE AWAY
I wrote this tool to
- Learn Nim
- Be a tool in my ctf toolbox
