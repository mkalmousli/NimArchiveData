
# Gitty

Easily create **`.gitignore`** in your terminal. I made this because when I am programming in Zig and other languages, where .gitignore's doesn't automatically get created, i can easily create them in my terminal.

![GitHub License](https://img.shields.io/github/license/chrischtel/gitty)



## Appendix

This readme and the project are barley finished. This project is very young.


## Installation

Make sure to have **`nim`** installed on your system.

```bash
  nimble install gitty
  
  # If you want to install globally on your system
  nimble install gitty -g
```
    
## Building from source
```bash
    $ git clone https://github.com/chrischtel/gitty.git # Clone the repository

    $ cd gitty

    $ nimble build -d:release -d:ssl # Build the project
```

You can now move the executable to `/usr/local/bin` or to any other **PATH** directory

## Usage/Examples

```bash
    gitty rust # creates a .gitignore for rust
    gitty rust nim # creates a .gitignore for rust and nim
```


## Roadmap
### This list is constantly updated

- Installation script, install without the need of nim installed on your system

- Custom **.gitignores**

