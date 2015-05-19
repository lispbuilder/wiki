

# About #

The goal for LISPBUILDER-SDL is to become a useful resource for the development of games in Lisp.  LISPBUILDER-SDL provides a set of bindings and Lispy abstractions to graphics, sound, physics, character animation and 3D libraries allowing development of interactive application and games in Common Lisp.

# Introduction #

The LISPBUILDER-SDL core functionality includes window and event loop management, support for 2D bitmap and vector graphics, 2D graphical effects such as rotation, color keying and alpha blending, many graphics drawing primitives such as circles, polygons, squares, Bezier and Cuttmull-Rom curves, BMP image support, playback of WAV samples and streaming music, and bitmap font support.

The core LISPBUILDER-SDL functionality described above can be enhanced by using [optional packages](optionalPackages#Optional_Packages) that provide additional capabilities such as True-type font support, 3D scene management, physics, character animation, support for loading diverse image formats (jpeg, png, tiff, tga, bmp etc.), a sound mixer, sample and music streaming for mp3 and OGG formats, rendering speed ups for many of the graphics primitives, and anti-aliasing support.

In addition, it is possible to [create standalone executables](UsingLispbuilderSDL#Creating_Standalone_Executables).

# Example Code #

```
(sdl:with-init ()
  (sdl:window 320 240)
  (sdl:draw-surface (sdl:load-image "lisp.bmp"))
  (sdl:update-display)
    (sdl:with-events ()
      (:quit-event () t)
      (:video-expose-event () (sdl:update-display))))
```

![https://lispbuilder.github.io/documentation/lispbuilder-sdl/documentation/sdl-alien.png](https://lispbuilder.github.io/documentation/lispbuilder-sdl/documentation/sdl-alien.png)
