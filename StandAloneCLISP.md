

# Introduction #

  * Tutorial #1: Anthony Fairchild's CLISP HOWTO


# Tutorial #1 #

[Anthony's original post](http://article.gmane.org/gmane.lisp.cl-lispbuilder.general/522/match=executable)

This is a how-to for building a distributable game(standalone executable) using CLISP and lispbuilder-SDL on Windows.

  * Make sure everything works.  Try to run an example.  See the game run.  If the game does not run properly, troubleshoot, otherwise continue.

```
[1]> (asdf:operate 'asdf:load-op :lispbuilder-sdl-examples)
... lots of loading text ...
0 errors, 0 warnings
NIL
[2]> (sdl-examples:mandelbrot)
NIL
```

  * Create a directory to put all of the distributable files in. For example:

```
[3]> (ext:make-dir  "C:\\appz\\")
T
[4]> (ext:make-dir  "C:\\appz\\mandelbrot\\")
T
```

  * Create a main entry point for your game.  We will simply used the Squashed game as an example.  Create the `SQUASHED-MAIN` function:

```
[5]> (defun mandelbrot-main ()
       "Mandelbrot: main entry function"

        (cffi:define-foreign-library sdl
          (t (:default "SDL")))

        (cffi:use-foreign-library sdl)

        (sdl:with-init ()
          (sdl-examples:mandelbrot))
          (ext:quit))
MANDELBROT-MAIN
```

  * Next, create the standalone executable. **One important thing to note here is you must call `SAVEINITMEM` from the CLISP prompt and not `SLIME`.  If you do it from SLIME you will get a socket error in your executable.**

```
[6]> (ext:saveinitmem "c:\\appz\\mandelbrot\\mandelbrot.exe"
                 :init-function #'mandelbrot-main
                 :NORC t
                 :script t
                 :executable t
                 :quiet t)
2881776 ;
720444
```

  * Copy other support files to the distribution directory:

```
[7]> (ext:copy-file "C:\\clisp-2.41\\full\\*.dll" "C:\\appz\\mandelbrot\\")

((#P"C:\\<CLISP installation directory>\\full\\libiconv-2.dll"
  #P"C:\\appz\\mandelbrot\\libiconv-2.dll" 1410765)
 (#P"C:\\<CLISP installation directory>\\full\\libintl-8.dll" 
  #P"C:\\appz\\mandelbrot\\libintl-8.dll" 1888606)
 (#P"C:\\<CLISP installation directory>\\full\\readline5.dll" 
  #P"C:\\appz\\mandelbrot\\readline5.dll" 229888))

[8]> (ext:copy-file "C:\\windows\\system32\\SDL.dll" "C:\\appz\\mandelbrot\\")

((#P"C:\\WINDOWS\\system32\\SDL.dll" #P"C:\\appz\\mandelbrot\\SDL.dll" 258048))
```

  * Try running `C:\appz\mandelbrot\mandelbrot.exe`.  If it runs then you can now distribute the files located in C:\appz\mandelbrot\.  Note that the CLISP console is present when running the game.  To hide the console screen take a look [here](http://www.frank-buss.de/lisp/clisp.html).

  * Now go make some games!
