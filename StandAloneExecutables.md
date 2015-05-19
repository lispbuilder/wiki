

# Introduction #

All modern Lisp compilers can create executables to be distributed to users. This page covers how to build executables under various implementations, and how to package and distribute them for various OSes.

# Prerequisite: Ensure LISPBUILDER-SDL is Working #

Make sure everything works. Try an example; see the game run. If the game does not run properly, troubleshoot, otherwise continue.

<pre>
> (asdf:operate 'asdf:load-op :lispbuilder-sdl-examples)<br>
... lots of loading text ...<br>
NIL<br>
> (sdl-examples:mandelbrot)<br>
NIL<br>
</pre>

# Windows Prerequisite: Cygwin, MSYS, or GnuWin32 #

The guide assumes a Unix-like command-line environment. It is not strictly necessary to use such an environment for development, although it may make your life easier (in more ways that one). Feel free to use batch commands or Power Shell, but be aware that you may have to translate some simple shell commands.

If you are interested in installing a Unix-like shell on Windows, [Cygwin](http://www.cygwin.com/), [MSYS](http://www.mingw.org/), and [GnuWin32](http://gnuwin32.sourceforge.net/) are all good options.

# Defining a Startup Function #

When creating an executable, a user-defined function can be provided to run when the Lisp image starts up. Users of LISPBUILDER-SDL typically use this function to load and initialize SDL and start their game. An example of such a function follows;

<pre>
(defun main ()<br>
<br>
;; Load SDL<br>
(cffi:define-foreign-library sdl<br>
(t (:default "SDL")))           ; Windows only, see below for portable version<br>
(cffi:use-foreign-library sdl)<br>
<br>
;; Run the game<br>
(sdl:with-init ()<br>
(sdl-examples:mandelbrot))<br>
<br>
;; Quit<br>
#-allegro (quit) #+allegro (exit))<br>
</pre>

The function looks for a foreign library called SDL (e.g. "SDL.dll"), and loads the library. Then it starts one the examples in LISPBUILDER-SDL. When the example demo ends, it quits the program (to avoid entering the toplevel, as some Lisps do).

Type the above function into a file called "main.lisp" for use below.

# Dumping an Executable #

Executables, or Lisp images, are executable copies of the runtime state of the compiler after loading user-defined code. Some Lisp compilers, especially commercial ones, try to slim down the resulting executable, but in open source compilers, an image will include the entire compiler.

To dump an executable, first start the compiler and load LISPBUILDER-SDL and any user code. After defining a startup function (more details below), run the following commands to create the binary.

<pre>
> (asdf:operate 'asdf:load-op :lispbuilder-sdl)<br>
...<br>
NIL<br>
> (load (compile-file "main"))<br>
...<br>
NIL<br>
> (let ((filename #+windows "main.exe" #-windows "main"))<br>
#+clisp (saveinitmem filename :init-function #'main :executable t :norc t)<br>
#+sbcl (save-lisp-and-die filename :toplevel #'main :executable t)<br>
#+clozure (save-application filename :toplevel-function #'main :prepend-kernel t))<br>
<br>
;; TODO: Add Lispworks and Allegro.<br>
</pre>

Theoretically, this executable can just be run by itself,

<pre>
$ ./main   # or main.exe on Windows<br>
</pre>

but, more often than not, the necessary runtime components (shared libraries, graphics, etc) are not in correct locations relative to the executable. Therefore, for most applications, it will be necessary to build a distribution to run on each target platform.

# Bundling Shared Libraries #

The easiest way to find runtime libraries is simply to put them in the same directory as the binary. For example;

<pre>
$ mkdir build<br>
$ cp main build<br>
</pre>

Find SDL.dll (on Windows), libSDL.so (on Linux), or SDL.framework (on OS X), and copy it to the build folder.

<pre>
$ cp C:\\path\\to\\SDL.dll build             # Windows<br>
$ cp /usr/lib/libSDL.so build                # Linux<br>
$ cp /Library/Frameworks/SDL.framework build # OS X<br>
</pre>

And copy and runtime libraries required by the compiler itself. For example, on CLISP;

<pre>
$ cp C:\\path\\to\\clisp\\*.dll   # CLISP<br>
</pre>

Finally, copy and graphics or other resources to the build folder. Now try running the game;

<pre>
$ ./main<br>
</pre>

If it works, copy the folder over to another machine (preferably one without Lisp or SDL installed), and try running it. If it works there too, then you can tar it, zip it, make a Mac app for it, or make a Windows installer for it, etc.

# Writing a Better Startup Function #

One problem with the provided startup function is that expects to find a shared library called "SDL". This doesn't work on Linux where the file is traditionally "libSDL.so", or on OS X, where most users would prefer to use "SDL.framework". The following snippets, taken from the source code of LISPBUILDER-SDL, can be used as portable substitutes for the above.

<pre>
(cffi:define-foreign-library sdl<br>
(:darwin (:or (:framework "SDL")<br>
(:default "libSDL")))<br>
(:windows "SDL.dll")<br>
(:unix (:or "libSDL-1.2.so.0.7.2"<br>
"libSDL-1.2.so.0"<br>
"libSDL-1.2.so"<br>
"libSDL.so"<br>
"libSDL")))<br>
</pre>

https://lispbuilder.github.io/documentation/lispbuilder-sdl/cffi/library.lisp
