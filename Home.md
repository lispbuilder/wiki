# Introduction

Lispbuilder (short for Common Lisp Application Builder), is a
collection of cross-platform packages for building large, interactive
applications in Common Lisp. The most notable of these is
[Lispbuilder-SDL](LispbuilderSDL), a wrapper for
[SDL](https://libsdl.org/), a library commonly used for game
development. Other packages provide 3D graphics and animation,
networking, text processing, and other miscellaneous functionality.

The packages include:

  * Game development:
    * [lispbuilder-sdl](LispbuilderSDL): for SDL (including support libraries SDL_image, etc.)
    * lispbuilder-openrm: OpenRM 3d scene graph
    * lispbuilder-cal3d: Cal3d character animation
    * lispbuilder-opengl: deprecated, use [cl-opengl](https://common-lisp.net/project/cl-opengl/) instead
      * Note: OpenGL by itself does not provide windowing support; lispbuilder-sdl can be used with cl-opengl to provide windowing support for OpenGL

  * Text processing:
    * lispbuilder-clawk: an awk-like tool
    * lispbuilder-lexer: a flex-like lexer
    * lispbuilder-yacc: a yacc-like parser
    * lispbuilder-regex: for regular expressions

  * Networking:
    * lispbuilder-net: deprecated, use [usocket](https://common-lisp.net/project/usocket/) instead

  * Win32 specific (windows.h)
    * lispbuilder-windows

**Note:** The packages above can be used separately. There is no need
to install all Lispbuilder packages if, for example, you only want
[lispbuilder-sdl](LispbuilderSDL).

# Download & Installation

All packages can be installed with [Quicklisp](https://www.quicklisp.org/).

Dependencies for lispbuilder-sdl must be installed separately; see the
[installation instructions](DownloadInstallationIntro) details.

# Support

  * [Mailing List](http://groups.google.com/group/lispbuilder)
  * [Issue Tracker](https://github.com/lispbuilder/lispbuilder/issues)
  * [Pull Requests](https://github.com/lispbuilder/lispbuilder/pulls) are accepted!
