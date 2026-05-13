# Intro
View local nim package documentation. View 
# Install
nimble install nimdoc

# Dependencies
[Live Server](https://github.com/lomirus/live-server), preferred, but if not installed
will use python -m http.server

So, you need python at least

# Use
```
nimdoc -h                     # show help
nimdoc <pkg>                  # open docs for <pkg>
nimdoc                        # open listing of all generated packages
nimdoc -c fish | source       # add nimdoc completions to fish shell  
nimdoc -r cueconfig           # force generate and view cueconfig docs
nimdoc -R cueconfig           # force reload web server and view cueconfig docs 
nimdoc -d cueconfig           # dry run
nimdoc -p cueconfig           # generate docs with private symbols
nimdoc -Rrp cueconfig         # do it all
```