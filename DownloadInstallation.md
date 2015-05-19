

# Overview #

The most commonly used Common Lisp development environments are the Open Source:

  * [SBCL](http://www.sbcl.org/), and
  * [CLISP](http://clisp.cons.org/)

This section describes how to set up a working installation of these systems for Windows, OS X and Linux such that the examples in `LISPBUILDER-SDL` and _helper_ will load and run.

# Dependencies #

## External Libraries ##

The `LISPBUILDER-SDL` packages rely on several external dynamic linked libraries. These libraries are not included in the `LISPBIULDER-SDL` packages unless installation is via the Lisp Starter Kit. These libraries must be installed prior to loading any of the `LISPBUILDER-SDL` packages.

## Common Lisp Packages ##

  * **ASDF** is pre-installed for users of SBCL, OpenMCL, ECL, ACL, or Lispworks using Edi Weitz's Lisp Starter Pack. Otherwise, please refer to [ASDF README](http://cclan.cvs.sourceforge.net/viewvc/*checkout*/cclan/asdf/README?revision=1.40) for installation instructions.
  * **CFFI** can be automatically downloaded and installed when using [ASDF-INSTALL](http://www.cliki.net/ASDF-Install). Otherwise, please refer to the [official installation instructions](http://common-lisp.net/project/cffi).

# Download & Installation of LISPBUILDER-SDL #

The latest version can always be obtained using [Subversion](http://subversion.tigris.org/). Use the instructions to sync to the latest version by clicking the **Source** tab above.

## Windows ##

  * See [Windows Installation](WindowsInstallation).

## OS X ##

In addition to downloading and installing `LISPBUILDER-SDL`, you need to install the `SDL` framework [available here](http://www.libsdl.org/). To use `SDL` you first need to compile the OS-X specific helper library located in `"lispbuilder-sdl/cocoahelper`"; just cd to this directory and type `"make"`. Then before trying to run any `SDL` programs you need to load the cocoahelper package:

```
(asdf:operate 'asdf:load-op :cocoahelper)
```

This creates an instance of `NSApplication` that serves as a bridge between `SDL` and Cocoa, similiar to that used by the Python project Pygame. If this is successful, you should see the icon for a generic OS-X ap appear in your dock.

## Linux ##

These instructions have been tested with the following version of Ubuntu and SBCL. Please open an issue or just email the project maintainers if you have difficulties with other versions.

Ubuntu 2.6.24 using SBCL 1.0.15

If you don't have SBCL then install that first. You should get slime and emacs too.

```
sudo apt-get install sbcl slime emacs
```

Once that is installed open up emacs. You need to add the following your .emacs to have SBCL and SLIME work. You'll need to change the path of slime from the one below, to where the slime package installs. In my case I just download the latest version of slime to that folder.

```
(add-to-list 'load-path "~/slime/")
(load "slime.el")

(slime-setup)
OR FOR LATER VERSIONS OF SLIME...
(slime-setup '(slime-repl))

(setq inferior-lisp-program "sbcl")
```

Once in emacs just type M-x slime to get sbcl running.

Now we need to install some dependencies. Lispbuilder-SDL uses CFFI; a portable way to interface with other programs. If SBCL installed correctly then you need to type the following to install CFFI using ASDF:

```
(require 'asdf)
(require 'asdf-install)
(asdf-install:install :cffi)
```

If everything is OK you should see a text menu:

```
CL-USER> (asdf-install:install :cffi)
Install where?
1) System-wide install: 
   System in /usr/local/lib/sbcl/site-systems/
   Files in /usr/local/lib/sbcl/site/ 
2) Personal installation: 
   System in /home/justinhj/.sbcl/systems/
   Files in /home/justinhj/.sbcl/site/ 
```

I usually install just for me by hitting 2. If everything is ok, ASDF-INSTALL will go the cliki web site and download the latest release of the CFFI package. It is dependent on babel , alexandria, trivial-features.

If you get GPG errors whilst it downloads, you either hit 0 to ignore, which is a potential security risk, or go to [and GPG](http://www.cliki.net/ASDF-Install) to find out about GPG.

Now you need to get the latest lispbuilder from svn. See http://code.google.com/p/lispbuilder/source/checkout

As a final step, at runtime we need to tell ASDF where the lisbuilder code is, and load it up.

```
(pushnew "/home/justinhj/lispbuilder/trunk/lispbuilder-sdl/" asdf:*central-registry* :test #'equal)
(pushnew "/home/justinhj/lispbuilder/trunk/lispbuilder-sdl-mixer/" asdf:*central-registry* :test #'equal)

(asdf:oos 'asdf:load-op 'lispbuilder-sdl)
(asdf:oos 'asdf:load-op 'lispbuilder-sdl-examples)
```

If that all works you can now try an example:

```
(lispbuilder-sdl-examples:mandelbrot)
```

## BSD ##

# Download and Installation of the Optional Packages #

## SDL\_gfx ##

Many drawing functions can be sped up if SDL\_gfx is installed [SDL\_gfx](http://www.ferzkopp.net/Software/SDL_gfx-2.0/). Windows/Mac users should download and install the binaries from [SDL\_gfx](http://www.ferzkopp.net/Software/SDL_gfx-2.0/). Linux users should use their package managers to install the RPM's or DEBs.

## SDL\_image ##

[SDL\_image](http://www.libsdl.org/projects/SDL_image/) when installed will add support for many more image formats to LISPBUILDER-SDL than just BMP. Windows/Mac users should download and install the binaries from [SDL\_image](http://www.libsdl.org/projects/SDL_image/). Linux users should use their package managers to install the RPM's or DEBs.

## lispbuilder-sdl-mixer ##

[SDL\_mixer](http://www.libsdl.org/projects/SDL_mixer/) must be installed in order for LISPBUILDER-SDL-IMAGE work. Windows users can use Edi Weitz's Lisp Starter Pack to install this library. Otherwise, Windows/Mac users should download and install the binaries from [SDL\_mixer](http://www.libsdl.org/projects/SDL_mixer/). Linux users should use their package managers to install the RPM's or DEBs.

## lispbuilder-sdl-ttf ##

  * [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) must be installed in order for LISPBUILDER-SDL-TTF work. Windows users can use Edi Weitz's Lisp Starter Pack to install  this library. Otherwise, Windows/Mac users should download and install [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) from [here](http://www.libsdl.org/projects/SDL_ttf/). Linux users should use their package managers to install the RPM's or DEBs.
  * In addition a glue library `lispbuilder-sdl-ttf-glue` must also be installed in the library search path. `lispbuilder-sdl-ttf-glue` wraps the font rendering functions in [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/) as these functions require struct passing by value; CFFI only supports passing structs by reference. Windows users can use Edi Weitz's Lisp Starter Pack to install the glue library. Otherwise the glue library must be built as follows;

### Windows Build Instructions ###

To build the glue library:

  1. A working installation of MingGW is required.
  1. Make sure that the path to the include files for `SDL` and `SDL_TTF` are specified correctly in `LISPBUILDER-SDL-TTF/Makefile.mingw`. Do this by modifying the line `SDL_TTF_CFLAGS=$(CFLAGS) -ISDL-1.2.11/include -ISDL_ttf-2.0.8`.
  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make` to create the glue library.
  1. If the build is successful, the `lispbuilder-sdl-ttf-glue` library will be found in `LISPBUILDER-SDL-TTF/BUILD/`.
  1. Copy or move the file `LISPBUILDER-SDL-TTF/BUILD/lispbuilder-sdl-ttf-glue.dll` to `LISPBUILDER-SDL-TTF/BIN/lispbuilder-sdl-ttf-glue.dll`

### Linux Build Instructions ###

To rebuild the glue library:

  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make` to create the glue library.
  1. If the build is successful, the `lispbuilder-sdl-ttf-glue` library will be found in the `LISPBUILDER-SDL-TTF/BUILD/` directory.
  1. In the `LISPBUILDER-SDL-TTF/` directory, execute `make install_ttf` to install the `lispbuilder-sdl-ttf-glue` library to the default library search path.

## lispbuilder-sdl-net ##

[SDL\_net](http://www.libsdl.org/projects/SDL_net/) must be installed in order for LISPBUILDER-SDL-NET work. Windows users can use Edi Weitz's Lisp Starter Pack to install this library. Otherwise, Windows/Mac users should download and install the binaries from [SDL\_net](http://www.libsdl.org/projects/SDL_net/). Linux users should use their package managers to install the RPM's or DEBs.
