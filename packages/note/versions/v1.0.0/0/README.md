# note
a simple pasteBin  

Heavily inspired by [bin](https://github.com/w4/bin), infact, the HTML templates are effectively just copied over with some tweaks... because they're awesome :-)  
  
_note_ aims to be a minimalist binary, with very little that needs to be handled. Just run the binary and point your reverse proxy to the right port and away you go!  
  
## How to use
Simply build the binary and run it (with the `--bind-addr` arg if you want)  
```sh
git pull https://codeberg.org/pswilde/note
cd note
nimble build
./note --bind-addr 0.0.0.0:8820
```
If you want to run behind a virtual directory, i.e. so requests can be sent to a virtual dir of another webpage (via a reverse proxy) instead of the root directory, you can use the `--virt-dir` parameter.  
```sh
./note --virt-dir /note
# Web server will only respond to requests with a path starting with "/note"
```

_notes_ are stored in a table within the runtime. No persistance is currently in place, nor required as far as I'm concerned. Effectively, all notes are lost when the binary is restarted.  

There's an info note at `/info` too

## To do's

- [x] change some colours in the template so it's a little different to bin
- [x] set root folder var, so it can be hosted behind a virtual directory if required
- [ ] check on generated id to ensure it doesn't match and current notes
- [x] add syntax highlighting like in bin (bit of a cheat, using [prism.js]https://prismjs.com) )
- [ ] curl support like in bin
- [ ] monitor cache size, and potentially limit it if it gets too unwieldly
- [ ] potential persistance (probably using something like redis)





