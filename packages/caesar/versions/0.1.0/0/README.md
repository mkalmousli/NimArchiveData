# caesar
A caesar cipher encoding and decoding tool.

# Installation
``` nim
nimble install caesar
```

# Usage
``` bash
caesar -h
```

# Help Output
``` bash
        ===============================================================         
               Caesar 0.1.0 - Caesar cipher encoding and decoding               
        ===============================================================         
 Encode or Decode file in Caesar cipher.

 
Usage: caesar <FILE> [OPTIONS] 

 Input may be a file path or stdin

 When no FILE is provided, read from standard input

                              ===== ARGUMENT =====                              
 <FILE>                       File for caesar cipher encoding/decoding.

                              ===== OPTIONS =====                               
 -k, --key NUM                Key for cipher encoding or decoding [default: 3]
 -d, --decode                 Decode caesar cipher
 -h, --help                   Show this help.
 -v, --version                Show version information.


                             ====== EXAMPLES =====                              
  # Encoding
    caesar my_file.txt -k 23

  # Text Encoding
    echo 'hello world' | caesar -k 23

  # Decoding
    caesar enc_file.txt -d -k 5

  # Text Decoding
    echo 'uryyb jbeyq' | caesar -k 13 -d

 NOTE: input containing spaces or special characters should be placed in quotes.


```
