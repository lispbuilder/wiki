

# Overview #

This page describes the installation and configuration of Lispworks and LISPBUILDER-SDL for Windows.

# Install Lispworks with the Lisp Starter Pack #

The LISPBUILDER-SDL package relies on the external [libSDL](http://www.libsdl.org/) dynamic linked library. [libSDL](http://www.libsdl.org/) is downloaded and installed automatically using the procedure describe below.

  1. Download & install [Lispworks Personal Edition](http://www.lispworks.com/downloads/index.html).
  1. Download & install [Edi Weitz's Lisp Starter Pack](http://www.weitz.de/starter-pack/).
  1. Download & save [config.lisp](http://lispbuilder.googlecode.com/files/config.lisp) to the same location as the Lisp Starter Pack. _Note:_ `LispStarterPack.exe` and `config.lisp` must be in the same directory.
  1. Run `LispStarterPack.exe` and select the `LISPBUILDER-*` packages. The packages will be downloaded and installed (in `Win XP`) to  `"My Documents\Lisp Libraries"`.

# Using LISPBUILDER-SDL #

  1. Start `Lispworks`.
  1. Use the `File menu` to `Load...` (and _not_ `Open...`) the file `start.lisp` that was installed in a directory called `"Lisp Libraries"` within the `"My Documents"` folder. Lispworks will now compile the packages.
  1. Execute the following in the Lispworks `"Listener"` (the REPL) window when compilation is `DONE`.
    * `(ASDF:OPERATE 'ASDF:LOAD-OP :LISPBUILDER-SDL-EXAMPLES)`
  1. Run one of the examples to verify that the installation was successful;
    * `(SDL-EXAMPLES:PARTICLES)`

ASDF will take care of loading the `CFFI` and `:LISPBUILDER-SDL` dependencies. The `SDL.dll` library will also be loaded into the Lisp image at this time.
