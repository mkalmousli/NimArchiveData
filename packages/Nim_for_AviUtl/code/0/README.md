This is a library for developing AviUtl's dll script.

How to install
```
nimble install Nim_for_Aviutl
```

How to use  
This is the template for creating a DLL script.
```Nim
import luanim51
import Nim_for_Aviutl

# function to be called from AviUtl
makeAviUtlfunc():
    proc example_func(L: ptr lua_State): cint {.cdecl, exportc.} =
      return 1
```
There are detailed examples in /example.

**Attention**  
Please place the nim.cfg file located in /src or /example directly within your project directory.  
This is because the compilation options cannot be specified within this library alone.
