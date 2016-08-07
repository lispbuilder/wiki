# Common Lisp Source

All packages can be installed with
[Quicklisp](https://www.quicklisp.org/). Quicklisp manages transitive
dependencies on Common Lisp libraries, so the following will download
all required dependencies.

```
(ql:quickload :lispbuilder-sdl-examples)
(sdl-examples:mandelbrot)
```

# Dependencies

Quicklisp does not manage the installation of external C libraries;
currently, these must be installed manually or via a package
manager. The above command will fail until these libraries are
properly installed.

## Windows

  * See [Windows Installation](WindowsInstallation).

## macOS

You can install [SDL](http://www.libsdl.org/) either manually (by
downloading the version 1.2 binaries from the website and copying
`SDL.framework` to `/Library/Frameworks`) or via a package manager
such as Homebrew or MacPorts. However, please note that package
managers have some known issues: for example, Homebrew does not build
a `.framework` (only a `.dylib`); and MacPorts builds with hard-coded
absolute paths (which will not be portable to other machines).

Due to certain limitations of SDL, you will also need to install the
*cocoahelper* helper library (which is included in the lispbuilder-sdl
source). This libary must be compiled from source. Find the directory
`lispbuilder-sdl/cocoahelper` inside your installation directory; cd
to this directory and type `make`. (XCode is required for this step.)

Whenever you run a lispbuilder-sdl application, cocoahelper will be
loaded automatically. For example, try one of the lispbulder-sdl
examples:

```
(ql:quickload :lispbuilder-sdl-examples)
(sdl-examples:mandelbrot)
```

This should creates an instance of `NSApplication` to serve as a
bridge between SDL and Cocoa, similiar to that used by the Python
project Pygame. If this is successful, you should see the icon for a
generic macOS app appear in your dock (following by the application
window itself).

## Linux and BSD

Most everything should be available via your package manager. For
example, in Ubuntu, the following should work:

```bash
sudo apt-get install libsdl1.2-dev libsdl-gfx1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev
```

Then try one of the lispbuilder-sdl examples:

```
(ql:quickload :lispbuilder-sdl-examples)
(sdl-examples:mandelbrot)
```

# Download and Installation of the Optional Packages #

## SDL\_gfx

Many drawing functions can be sped up if SDL\_gfx is installed [SDL\_gfx](http://www.ferzkopp.net/Software/SDL_gfx-2.0/). Windows/Mac users should download and install the binaries from [SDL\_gfx](http://www.ferzkopp.net/Software/SDL_gfx-2.0/). Linux users should use their package managers to install the RPM's or DEBs.

## SDL\_image

[SDL\_image](http://www.libsdl.org/projects/SDL_image/) when installed will add support for many more image formats to LISPBUILDER-SDL than just BMP. Windows/Mac users should download and install the binaries from [SDL\_image](http://www.libsdl.org/projects/SDL_image/). Linux users should use their package managers to install the RPM's or DEBs.

## lispbuilder-sdl-mixer

[SDL\_mixer](http://www.libsdl.org/projects/SDL_mixer/) must be installed in order for LISPBUILDER-SDL-IMAGE work. Windows users can use Edi Weitz's Lisp Starter Pack to install this library. Otherwise, Windows/Mac users should download and install the binaries from [SDL\_mixer](http://www.libsdl.org/projects/SDL_mixer/). Linux users should use their package managers to install the RPM's or DEBs.

## lispbuilder-sdl-ttf

  * [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) must be installed in order for LISPBUILDER-SDL-TTF work. Windows users can use Edi Weitz's Lisp Starter Pack to install  this library. Otherwise, Windows/Mac users should download and install [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) from [here](http://www.libsdl.org/projects/SDL_ttf/). Linux users should use their package managers to install the RPM's or DEBs.
  * In addition a glue library `lispbuilder-sdl-ttf-glue` must also be installed in the library search path. `lispbuilder-sdl-ttf-glue` wraps the font rendering functions in [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) as these functions require struct passing by value; CFFI only supports passing structs by reference. Windows users can use Edi Weitz's Lisp Starter Pack to install the glue library. Otherwise the glue library must be built as follows;

### Windows Build Instructions

To build the glue library:

  1. A working installation of MingGW is required.
  1. Make sure that the path to the include files for SDL and SDL_ttf are specified correctly in `LISPBUILDER-SDL-TTF/Makefile.mingw`. Do this by modifying the line `SDL_TTF_CFLAGS=$(CFLAGS) -ISDL-1.2.11/include -ISDL_ttf-2.0.8`.
  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make` to create the glue library.
  1. If the build is successful, the `lispbuilder-sdl-ttf-glue` library will be found in `LISPBUILDER-SDL-TTF/BUILD/`.
  1. Copy or move the file `LISPBUILDER-SDL-TTF/BUILD/lispbuilder-sdl-ttf-glue.dll` to `LISPBUILDER-SDL-TTF/BIN/lispbuilder-sdl-ttf-glue.dll`

### Linux Build Instructions

To rebuild the glue library:

  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make` to create the glue library.
  1. If the build is successful, the `lispbuilder-sdl-ttf-glue` library will be found in the `LISPBUILDER-SDL-TTF/BUILD/` directory.
  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make install_ttf` to install the `lispbuilder-sdl-ttf-glue` library to the default library search path.

## lispbuilder-sdl-net

[SDL\_net](http://www.libsdl.org/projects/SDL_net/) must be installed in order for LISPBUILDER-SDL-NET work. Windows users can use Edi Weitz's Lisp Starter Pack to install this library. Otherwise, Windows/Mac users should download and install the binaries from [SDL\_net](http://www.libsdl.org/projects/SDL_net/). Linux users should use their package managers to install the RPM's or DEBs.
