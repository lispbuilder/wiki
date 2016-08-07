# Dependencies

The various Lispbuilder libraries depend on their respective C counterparts:

  * lispbuilder-sdl depends on [SDL](https://libsdl.org/)
  * lispbuilder-sdl-gfx depends on [SDL_gfx](http://www.ferzkopp.net/wordpress/2016/01/02/sdl_gfx-sdl2_gfx/)
  * lispbuilder-sdl-image depends on [SDL_image](https://www.libsdl.org/projects/SDL_image/release-1.2.html)
  * lispbuilder-sdl-mixer depends on [SDL_mixer](https://www.libsdl.org/projects/SDL_mixer/release-1.2.html)
  * lispbuilder-sdl-ttf depends on [SDL_ttf](https://www.libsdl.org/projects/SDL_ttf/release-1.2.html)

*Note:* Currently lispbuilder-sdl only supports SDL 1.2. For SDL 2.0
support, see e.g. [cl-sdl2](https://github.com/lispgames/cl-sdl2).

Detailed instructions for [Windows](WindowsInstallation) and [macOS,
Linux and BSD](DownloadInstallation) describe OS-specific issues.

# Installing

All packages can be installed with [Quicklisp](https://www.quicklisp.org/). For example:

```
(ql:quickload :lispbuilder-sdl-examples)
(sdl-examples:mandelbrot)
```

# Compatibility

Lispbuilder should be compatible with all modern Common Lisp
implementations. Lispbuilder has been tested with SBCL, Clozure CL,
CMUCL, CLISP, ECL, Lispworks, and Allegro.

If you are new to Common Lisp and don't know which implementation to
choose, try [SBCL](http://sbcl.org/). All the implementations above
are good choices, but SBCL has a particularly active community.

**Note:** Clozure CL on macOS requires a [single-threaded
build](https://common-lisp.net/project/qitab/) to avoid thread safety
issues with Cocoa.
