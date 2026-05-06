# caesar
A caesar cipher encoding and decoding tool.

# Installation
``` nim
nimble install caesar
```

# Pattern Reference
[pattern reference file](https://github.com/ph4mished/caesar/pattern.md)


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
 -p, --pattern PATTERN        Advanced key patterns for cipher encoding or decoding(progressive cipher). Use 'caesar pattern --help' for complete reference
 -c, --count                  Count all characters (including non-alpha-ascii letters) for position index.
 -d, --decode                 Decode caesar cipher
 -h, --help                   Show this help.
 -v, --version                Show version information.


                                                         ====== EXAMPLES =====                                                         
  # Encoding
    caesar my_file.txt -k 23

  # Text Encoding
    echo 'hello world' | caesar -k 23
  # Progressive Cipher Encoding
    echo 'hello world' | caesar -p 13:1

  # Decoding
    caesar enc_file.txt -d -k 5

  # Text Decoding
    echo 'uryyb jbeyq' | caesar -k 13 -d

  # Progressive Cipher Decoding
    echo 'usabf ohlgz' | caesar -p 13:1 -d

 NOTE: input containing spaces or special characters should be placed in quotes.


```


# Pattern Help
```bash
caesar pattern --help
```
## Output
```bash
        ===============================================================         
                        Caesar 0.1.0 - Pattern Reference                        
        ===============================================================         
 Advanced key patterns for progressive and variant Caesar ciphers.

 
Usage: caesar --pattern PATTERN <FILE> [OPTIONS] 

                           ===== PATTERN SYNTAX =====                           
1. LINEAR INCREMENT
 KEY:STEP                     Add STEP to KEY for each letter.
Example: '0:1'                0,1,2,3,4,5...
Example: '25:-1'              25,24,23,22...(count down)
Example: '3:2'                3,5,7,9,11...

2. CYCLIC INCREMENT
 KEY:STEP:CYCLE               Add STEP to KEY for each letter, reset every CYCLE
Example: '3:1:5'              3,4,5,6,7, 3,4,5,6,7...
Example: '0:3:4'              0,3,6,9, 0,3,6,9...

3. BLOCK PATTERN
 KEY:STEP::BLOCK              Same KEY for BLOCK chars, increment by STEP
Example: '5:1::3'             5,5,5, 6,6,6, 7,7,7...
Example: '3:2::4'             3,3,3,3, 5,5,5,5, 7,7,7,7...

4. CUSTOM SEQUENCE
 A,B,C,D...                   Repeat exact sequence of keys
Example: '7,3,9'              7,3,9, 7,3,9, 7,3,9...
Example: '1,4,9,16'           1,4,9,16, 1,4,9,16, 1,4,9,16...

                         ===== POSITION COUNTING =====                          
By default, patterns use LETTER-ONLY position counting:
 'a_b' => 'a'=position0, 'b'=position1 (underscore ignored)

Use --count flag for ALL-CHARACTER counting:
 'a_b' => 'a'=position0, 'b'=position2 (underscore counts as position1)

                        ====== PRACTICAL EXAMPLES =====                         
  # Common CTF Patterns
    caesar -p 0:1 my_file.txt     #Increment by 1 each letter.
    caesar -p 3:1:5 message.txt     #Decode cyclic pattern.
    caesar -p 5:1::3 flag.txt     #Same key for 3 chars.
    caesar -p 7,3,9 puzzle.txt     #Custom sequence

  # With Position Counting Options
    caesar -p 0:1 my_file.txt -c     #Count all
    caesar -p 0:1 message.txt    #Count only alpha-ascii letters

  # Mathematical Patterns
    '0:1'   = f(n) = n     #Linear.
    '3:1:5' = f(n) = 3 + (n mod 5)     #Modulo cycle.
    '0:2'   = f(n) = 2n     #Even Numbers.
    '1:2'   = f(n) = 2n + 1    #Odd Numbers.

                               ====== NOTE =====                                
 - Keys wrap automatically modulo 26
 - Non-alpha-ascii letters (spaces, numbers, punctuations) are never shifted
 - Patterns work with both uppercase and lowercase letters


```

**NOTE**: Caesar cipher is extremely weak, do notuse for anything that actually needs protection

# TAKE AWAY
I wrote this tool to
- Learn Nim
- Be a tool in my ctf toolbox
