Nimrod genie os module
======================

[Nimrod](http://nimrod-code.org) provides the [os
module](http://nimrod-code.org/os.html) with OS related procs to manage files
among other things. But this is really just basic POSIX stuff, and nowadays
OSes have things like recycle bins and speakers to play sounds.  However, these
kind of procs are not very cross platform and some people wouldn't like them in
the base os module.

So here's my answer, **genieos** contains several procs which are *too awesome*
to be included in the standard os one. You could actually think of this module
as a *fancy os module*, but if we are talking about fancy stuff, you can't get
more fancy than [Girls' Generation
소녀시대](http://en.wikipedia.org/wiki/Girls'_Generation), also known as
[Sonyeo Shidae](http://www.youtube.com/watch?v=EOWrdo1kVYw) or
[SNSD](http://www.youtube.com/watch?v=fYP_3QEb5Yk). Just [tell them your
wish](http://www.youtube.com/watch?v=wC58wPbgByA), you never know what this
module could do for you.



License
=======

[Seohyun](http://en.wikipedia.org/wiki/Seohyun) says this has a
[MIT](LICENSE.md) license.


Installation and usage
======================

You could copy the [genieos.nim](genieos.nim) file and [private](private)
directory to your project or put these somewhere safe and use [nimrod's
configuration files](http://nimrod-code.org/nimrodc.html#configuration-files)
feature to specify their path. But that would be really raw, like asking
[Jessica](http://en.wikipedia.org/wiki/Jessica_Jung) to eat a cucumber ([she
dislikes them!](http://www.youtube.com/watch?v=TUR7CuD_1zQ)). So you can use
[Nimrod's babel package manager](https://github.com/nimrod-code/babel) and
type:

    babel install genieos

Once you have the module *somewhere* you can use ``import genieos`` in your
source code and everything should be fine.


Documentation
=============

The genieos module comes with embedded docstrings.
[Sooyoung](http://en.wikipedia.org/wiki/Sooyoung) recommends you to run
``nimrod doc2 genieos.nim`` and obtain a reference html file with instructions
on the exported symbols.  If you installed through babel, you can find this in
a path similar to ``~/.babel/libs/genieos-version``.

The generated documentation can also be found at
[http://gradha.github.io/genieos/genieos.html](http://gradha.github.io/genieos/genieos.html).
No guarantees on its freshness, though, do check the generation date at the
bottom.


Extra binaries
==============

To put the *awesome* procs into use there's a **trash** command which works
like your typical **rm** from the commandline but instead of removing files it
puts them nicely in your recycle bin. Check out the [trash-binary
directory](trash-binary) for further information.


Roadmap
=======

This module implements MacOSX functionality, a toy OS
[Sunny](http://en.wikipedia.org/wiki/Sunny_(singer\)) would enjoy using. *Real
men* with *real operative systems*, however, are presumed to step in at some
point and implement these procs (or add new ones) to their *manly platforms*.
Unfortunately they are [figuring out where the recycle bin
is](http://stackoverflow.com/a/6807599/172690) or [cleaning tiles for some
reason](http://en.wikipedia.org/wiki/Windows_8).

Note how I carefully avoided any mention of snakes and dongles here... *oops*. Anyway, the released versions so far are:

 * 9.0.0 - [Taeyeon](http://en.wikipedia.org/wiki/Kim_Tae-yeon), initial
   release.


Feedback
========

You can send me feedback through [github's issue
tracker](https://github.com/gradha/genieos/issues). I also take a look from
time to time to [Nimrod's forums](http://forum.nimrod-code.org) where you can
talk to other *more serious* nimrod programmers.
