

# About LISPBUILDER-SDL #

LISPBUILDER-SDL provides abstractions to graphics, sound, physics (TBD), character animation (TBD) and 3D libraries (in development) allowing the development of interactive applications and games in Common Lisp.

Core functionality includes window and event loop management, support for 2D bitmap and vector graphics, 2D graphical effects such as rotation, color keying and alpha blending, many graphics drawing primitives such as circles, polygons, squares, Bezier and Cuttmull-Rom curves, vector drawing, image loading (bmp, jpeg, png, tiff, tga), sound mixing and playback of samples (WAV) and streaming music (mp3 and ogg formats), bitmap and true-type font support.

Optional packages allow the core functionality described above to be enhanced to provide additional capabilities such 3D scene management, physics and character animation.

# Introduction #

LISPBUILDER-SDL is not distributed with any Common Lisp development environment and must be downloaded and installed separately. Installation is always tricky for the Lisp newbie because Lisp packages are unlike similar packages for other languages, but we have attempted to provide [comprehensive instructions](http://code.google.com/p/lispbuilder/wiki/DownloadInstallationIntro) for all supported platforms. Support is also provided on the [Lispbuilder Mailing List](http://code.google.com/p/lispbuilder/wiki/Community). Before downloading, verify that your Lisp implementation and operating system [are supported](http://code.google.com/p/lispbuilder/wiki/DownloadInstallationIntro).

Once installed, LISPBUILDER-SDL must be loaded into your Common Lisp environment by following the instructions [here](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Loading_LISPBUILDER-SDL).

The structure of a simple game follows the pattern;

  1. [Initialize the library](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Initializing_LISPBUILDER-SDL)
  1. [Create a display](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#The_Display)
  1. [Start the game loop](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#The_Game_Loop) to [handle input events](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Introduction_to_Input_Handling), [perform the game logic](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Executing_Game_Logic), and [update the display](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Updating_the_Display)
  1. Goto to 3

The following example makes a box (a filled rectangle) follow the position of the mouse cursor, redrawing the box in a new color when the mouse button is depressed. Pressing any key will exit the example.

![https://lispbuilder.github.io/documentation/mouse-rect-2d.jpg](https://lispbuilder.github.io/documentation/mouse-rect-2d.jpg)

```
(defparameter *random-color* sdl:*white*)
(defun mouse-rect-2d ()
  (sdl:with-init ()
    (sdl:window 200 200 :title-caption "Move a rectangle using the mouse")
    (setf (sdl:frame-rate) 60)

    (sdl:with-events ()
      (:quit-event () t)
      (:key-down-event ()
       (sdl:push-quit-event))
      (:idle ()
       ;; Change the color of the box if the left mouse button is depressed
       (when (sdl:mouse-left-p)
         (setf *random-color* (sdl:color :r (random 255) :g (random 255) :b (random 255))))

       ;; Clear the display each game loop
       (sdl:clear-display sdl:*black*)

       ;; Draw the box having a center at the mouse x/y coordinates.
       (sdl:draw-box (sdl:rectangle-from-midpoint-* (sdl:mouse-x) (sdl:mouse-y) 20 20)
                     :color *random-color*)

       ;; Redraw the display
       (sdl:update-display)))))
```

First create a variable to hold the color.

```
(defparameter *random-color* sdl:*white*)
```

LISPBUILDER-SDL must be [initialized prior to use](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Initializing_LISPBUILDER-SDL). The example only draws to the screen and so there is no need to initialize the audio, joystick, or CDROM subsystems.

```
(sdl:with-init ()
```

Next, create a 200 pixel by 200 pixel [graphics window](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#The_Display). Some kind of display (window or full-screen) must be created to receive events from the Window Manager.

```
    (sdl:window 200 200 :title-caption "Move a rectangle using the mouse")
```

Lock the [frame-rate](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Setting_the_Frame_Rate) to 60 fps.

```
    (setf (sdl:frame-rate) 60)
```

Create the [game loop](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#The_Game_Loop) to process the incoming user events.

```
    (sdl:with-events ()
```

The event loop will exit only when the [:QUIT-EVENT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Quit_Event) handler returns `T`. Always remember to handle this event or the event loop will never exit. This handler is called when a quit event is received in the event queue. A quit event is generated when the user attempts to close the window, or any time [SDL:PUSH-QUIT-EVENT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#push-quit-event) is called.

```
      (:quit-event () t)
```

The [:KEY-DOWN-EVENT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Keyboard_Events) handler is called for each key depressed. We want to exit the example to exit when a key is pressed, therefore push the quit event onto the event queue [SDL:PUSH-QUIT-EVENT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#push-quit-event) causing `:QUIT-EVENT` to fire on the next event loop.

```
      (:key-down-event ()
       (sdl:push-quit-event))
```

Most game logic will be called from the [:IDLE event](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#IDLE_Event). This handler is called when all other events in the event queue have been processed for the current event loop. In other words, `:IDLE` is called when the game loop is 'idle' and as such :IDLE is not a real system event.

```
      (:idle ()
```

Generate a random color while the right mouse button is depressed. Keeping the mouse down will cause the colors to cycle. [MOUSE-LEFT-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-left-p) returns `T` when the right mouse button is depressed.

```
       (when (sdl:mouse-left-p)
         (setf *random-color* (sdl:color :r (random 255) :g (random 255) :b (random 255))))
```

Clear the screen prior to drawing the rectangle.

```
       (sdl:clear-display sdl:*black*)
```

Draw the box. A new rectangle is created having a midpoint at the current cursor position using [RECTANGLE-FROM-MIDPOINT-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle-from-midpoint-*). [MOUSE-X](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-x) and [MOUSE-Y](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-y) return the current mouse cursor position.

```
       (sdl:draw-box (sdl:rectangle-from-midpoint-* (sdl:mouse-x) (sdl:mouse-y) 20 20)
                     :color *random-color*)
```

The display is then redrawn at the end of the game loop.

```
       (sdl:update-display)))))
```

# Compatibility & Platforms Supported #

LISPBUILDER-SDL runs on all of the popular Common Lisp implementations. The [compatibility table](http://code.google.com/p/lispbuilder/wiki/DownloadInstallationIntro) lists the combinations of implementation and operating system that are supported.

# Loading LISPBUILDER-SDL #

After [installation](http://code.google.com/p/lispbuilder/wiki/DownloadInstallation#Download_&_Installation_of_LISPBUILDER-SDL) is complete, `LISPBUILDER-SDL` can be loaded as follows;

```
(ASDF:OPERATE 'ASDF:LOAD-OP :LISPBUILDER-SDL)
```

Or if you are using SLIME, enter `,` then `l` then `LISPBUILDER-SDL`

# Initializing LISPBUILDER-SDL #

`LISPBUILDER-SDL` **must** be initialized prior to use. Audio, video, joystick and CDROM are separate subsystems and must also be initialized prior to use. The following functions will initialize (and close) the `LISPBUILDER` library;

  * [WITH-INIT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-init), initialize/close the library and specified subsystems.
  * [INIT-SDL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#init-sdl), initialize the library and specified subsystems.
  * [QUIT-SDL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#quit-sdl), close the library and specified subsystems.

The video, audio, joystick and CDROM subsystems are automatically initialized as follows;

  * `WINDOW` will initialize the video subsystem when creating the display,
  * `OPEN-AUDIO` will initialize the audio subsystem when opening audio,
  * `OPEN-JOYSTICK` will initialize the joystick subsystem when opening the joystick for use _(not yet supported)_,
  * `OPEN-CDROM` will initialize the CDROM subsystem when opening the CDROM drive for use _(not yet supported)_

Individual subsystems can also be initialized using `INIT-SDL` and `WITH-INIT`.

```
(SDL:INIT-SDL :VIDEO T :AUDIO T)
```

or, [WITH-INIT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-init), by passing the subsystems as parameters, for example

```
(SDL:WITH-INIT (SDL:SDL-INIT-VIDEO SDL:SDL-INIT-AUDIO)
  ...)
```

The list of subsystems is as follows;

  * `SDL-INIT-VIDEO`, The video subsystem
  * `SDL-INIT-AUDIO`, The audio subsystem
  * `SDL-INIT-CDROM`, The cdrom subsystem
  * `SDL-INIT-JOYSTICK`, The joystick subsystem
  * `SDL-INIT-NOPARACHUTE`, Prevents `SDL` from catching fatal signals.

`INIT-SDL` will only initialize subsystems that are not already initialized. To force the initialization of subsystems regardless of current status use `:FORCE T`, for example

```
(SDL:INIT-SDL :VIDEO T :AUDIO T :FORCE T)
```

Video, audio, joystick and CDROM subsystems can be initialized at any time using the following functions;

  * `INIT-SDL`, initializes a list of subsystems not already initialized,
  * `INIT-VIDEO`, initializes the video subsystem if not already initialized,
  * `INIT-AUDIO`, initializes the video subsystem if not already initialized,
  * `INIT-CDROM`, initializes the CDROM subsystem if not already initialized,
  * `INIT-JOYSTICK`, initializes the joystick subsystem if not already initialized,

These functions will force the initialization of the specified subsystem regardless of current status when `FORCE` is `T`.

The status of the library or of any subsystem can be queried at runtime using;

  * `SDL-INIT-P`, returns a list of initialized subsystems,
  * `VIDEO-INIT-P`, returns `T` if the video subsystem is initialized,
  * `AUDIO-INIT-P`, returns `T` if the audio subsystem is initialized,
  * `CDROM-INIT-P`, returns `T` if the CDROM subsystem is initialized,
  * `JOYSTICK-INIT-P`, returns `T` if the joystick subsystem is initialized,

```
>> (SDL:SDL-INIT-P SDL:SDL-INIT-VIDEO)
((LISPBUILDER-SDL-CFFI::SDL-INIT-VIDEO 32))

>> (SDL:VIDEO-INIT-P)
T
```

Subsystems can be closed at any time using the following functions;

  * `QUIT-SDL`, closes the list of initialized subsystems or all initialized subsystems if unspecified,
  * `QUIT-VIDEO`, closes the video subsystem if initialized,
  * `QUIT-AUDIO`, closes the audio subsystem if initialized,
  * `QUIT-CDROM`, closes the CDROM subsystem if initialized,
  * `QUIT-JOYSTICK`, closes the joystick subsystem if initialized

These functions will force close of the specified subsystems regardless of current status when `FORCE` it `T`.

Using [WITH-INIT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-init) will automatically close lispbuilder-sdl upon exit.

# Hardware #
## Graphics Hardware Capabilities ##

`LISPBUILDER-SDL` can utilize the following capabilities of the graphics hardware if available;

  * Create surfaces in video memory,
  * Use a window manager if available,
  * Hardware acceleration for video memory to video memory blits,
    * For normal surfaces,
    * Or when color keying, or
    * When using alpha transparency,
  * Hardware acceleration for system memory to video memory blits,
    * For normal surfaces,
    * Or when color keying, or
    * When using alpha transparency,
  * Hardware acceleration for color fills,
  * OpenGL for 2D or 3D acceleration.

The capabilities of the video hardware can be queried following initialization of the video subsystem `(INIT-SDL :VIDEO T`), or `(INIT-VIDEO)`. Prior to the display being created (using `WINDOW`) these functions will return the best video mode available. After the display is created these functions will return the current video mode.

  * `HW-AVAILABLE-P`, is it possible to create hardware surfaces, or is the current video display in video memory?
  * `WM-AVAILABLE-P`, is there a window manager available?
  * `BLIT-HW-P`, are video memory to video memory blits accelerated?
  * `BLIT-HW-CC-P`, are video memory to video memory color key blits accelerated?
  * `BLIT-HW-A-P`, are video memory to video memory alpha blits accelerated?
  * `BLIT-SW-P`, are system memory to video memory blits accelerated?
  * `BLIT-SW-CC-P`, are system memory to video memory color key blits accelerated?
  * `BLIT-SW-A-P`, are system memory to video memory alpha blits accelerated?
  * `BLIT-FILL-P`, are color fills accelerated?
  * [VIDEO-INFO](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-info) queries the capabilities of the graphics hardware,
  * [VIDEO-MEMORY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-memory) returns the amount of video memory available,
  * [VIDEO-DIMENSIONS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-dimensions) returns the best video width and height supported by the graphics hardware, and
  * [LIST-MODES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#list-modes) returns a list of of window dimensions supported by the graphics hardware.
  * [VIDEO-DRIVER-NAME](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-river-name) returns the name of the video driver.

The video driver exposes hardware capabilities to the library. The `windib` display driver is used in Windows by default. This driver provides almost no support for the underlying hardware and will perform all graphical operations in software. However `windib` is the most compatible across hardware and operating system versions, and is usually the best choice in terms of speed especially when using color keys or alpha blending.

The video driver can be specified by passing the driver name to `WINDOW` using `:VIDEO-DRIVER`, or using `SET-VIDEO-DRIVER`. The display driver is set prior to creating the display.

  * [SET-VIDEO-DRIVER](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Video_Driver) sets the video driver.

The audio driver can be specified by passing the driver name to `WINDOW` using `:AUDIO-DRIVER`, or using `SET-AUDIO-DRIVER`. The display driver is set prior to creating the display.

  * [SET-AUDIO-DRIVER](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Audio_Driver) sets the audio driver.

## Video Info ##

Use [VIDEO-INFO](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-info) to query the capabilities of the graphics hardware.

```
video-info &optional info => result
```

`:INFO` can be one of:

  * `:NIL`, returns a list of all supported video hardware capabilities
  * `:HW-AVAILABLE`, Is it possible to create hardware surfaces?
  * `:WM-AVAILABLE`, Is there a window manager available?
  * `:BLIT-HW`, Are hardware to hardware blits accelerated?
  * `:BLIT-HW-CC`, Are hardware to hardware colorkey blits accelerated?
  * `:BLIT-HW-A`, Are hardware to hardware alpha blits accelerated?
  * `:BLIT-SW`, Are software to hardware blits accelerated?
  * `:BLIT-SW-CC`, Are software to hardware colorkey blits accelerated?
  * `:BLIT-SW-A`, Are software to hardware alpha blits accelerated?
  * `:BLIT-FILL`, Are color fills accelerated?

For example, enter the following to determine the capabilities of the video hardware;

```
>> (SDL:SET-VIDEO-DRIVER "windib")
0

>> (SDL:INIT-VIDEO)
T

>> (SDL:VIDEO-INFO))
(:WM-AVAILABLE)

>> (SDL:QUIT-SDL)

>> (SDL:SET-VIDEO-DRIVER "directx")
0

>> (SDL:INIT-VIDEO)
T

>> (SDL:VIDEO-INFO))
(:HW-AVAILABLE :WM-AVAILABLE :BLIT-SW-CC :BLIT-FILL :BLIT-HW-CC :BLIT-SW :BLIT-HW)

```

## Video Memory ##

Use [VIDEO-MEMORY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-memory) to query the amount of video memory available. Only makes sense when surfaces can be created in video memory, `(HW-AVAILABLE-P)` returns `T`, otherwise `0` video memory is reported.

For example;

```
>> (SDL:INIT-VIDEO)
T

>> (SDL:HW-AVAILABLE-P)
NIL

>> (SDL:VIDEO-MEMORY)
0
```

## Video Dimensions ##

[VIDEO-DIMENSIONS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-dimensions) returns the best video width and height if called before a window is created. Or returns the current video dimensions if called after a window is created.

For example;

```
>> (SDL:INIT-VIDEO)
T

>> (SDL:VIDEO-DIMENSIONS)
#(1280 800)
```

## List Modes ##

[LIST-MODES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#list-modes) returns a list sorted largest to smallest of the width and height of the screens that will support the type of video surface requested in `FLAGS`.

```
list-modes flags => result
```

`FLAGS` can be one or more of the following;

  * [SDL-SW-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-sw-surface) a video surface in system memory,
  * [SDL-HW-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-hw-surface) a video surface in video memory,
  * [SDL-ASYNC-BLIT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-async-blit) asynchronously updates supported,
  * [SDL-ANY-FORMAT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-any-format), see the Section Color Depth.
  * [SDL-HW-PALETTE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-hw-palette) exclusive palette access.
  * [SDL-DOUBLEBUF](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-doublebuf) hardware double buffering.
  * [SDL-FULLSCREEN](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-fullscreen) full-screen mode.
  * [SDL-OPENGL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-opengl) an OpenGL rendering context.
  * [SDL-RESIZABLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-resizable) a resizable window, and
  * [SDL-NO-FRAME](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#sdl-no-frame) no title bar or frame decoration.

For example;

```
>> (SDL:INIT-VIDEO)
T

>> (SDL:LIST-MODES SDL:SDL-HW-SURFACE SDL:SDL-FULLSCREEN SDL:SDL-DOUBLEBUF)
(#(1280 800) #(1024 768) #(800 1280) #(800 600) #(768 1024) 
 #(640 480) #(640 400) #(600 800) #(512 384) #(480 640) #(400 640) 
 #(384 512) #(320 200))
```

## Video Driver ##

`SET-VIDEO-DRIVER` will attempt to use the specified video driver to create the display. The video drivers supported are listed in the [SDL documentation](http://www.libsdl.org/cgi/docwiki.cgi/SDL_envvars). Set the video driver after the video subsystem has been initialized, but before the display is created.

Valid drivers for Windows are "windib" and "directx". The default video driver is "windib".

For example;

```
>> (SDL:SET-VIDEO-DRIVER "windib")
```

`:VIDEO-DRIVER` when passed to `WINDOW` will also set the video driver.

## Audio Driver ##

`SET-AUDIO-DRIVER` will attempt to use the specified audio driver. The audio drivers supported are listed in the [SDL documentation](http://www.libsdl.org/cgi/docwiki.cgi/SDL_envvars). Set the audio driver after the audio subsystem has been initialized, but before the display is created.

Valid drivers for Windows are "pulse", "dsound" and "waveout".

For example;

```
>> (SDL:SET-AUDIO-DRIVER "waveout")
```

`:AUDIO-DRIVER` when passed to `WINDOW` will also set the audio driver.

## Video Driver Name ##

`VIDEO-DRIVER-NAME` will return the name if the video driver. The video drivers supported are listed in the [SDL documentation](http://www.libsdl.org/cgi/docwiki.cgi/SDL_envvars). This function is useful only after the video subsystem has been initialized.

For example;

```
>> (SDL:INIT-VIDEO)
T

>> (SDL:VIDEO-DRIVER-NAME)
"windib"
```

## Audio Driver Name ##

`Audio-DRIVER-NAME` will return the name if the audio driver. The audio drivers supported are listed in the [SDL documentation](http://www.libsdl.org/cgi/docwiki.cgi/SDL_envvars). This function is useful only after the audio subsystem has been initialized.

For example;

```
>> (SDL:INIT-AUDIO)
T

>> (SDL:AUDIO-DRIVER-NAME)
"dsound"
```

# The Display #
## Description of the Display ##

Graphics are rendered to a video surface. A [SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#surface) represents an area of _graphical_ memory that can be drawn or rendered to, for example the video frame buffer or an image that is loaded from disk.

The video surface must be updated at least once each game loop using the function [UPDATE-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#update-display). The display can be filled with a background color using [CLEAR-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#clear-display).

The properties of an the vidoe surface can be queried using [VIDEO-INFO](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-info). The screen resolutions supported by a particular set of video flags can be retrieved using [LIST-MODES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#list-modes). The name of the video driver can be retrieved as a STRING using [VIDEO-DRIVER-NAME](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-driver-name).

Use [WINDOW](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#window) to create and configure the video surface on startup.

```
window width height 
       &key flags sw hw fullscreen async-blit any-format palette double-buffer 
            opengl opengl-attributes resizable no-frame 
            bpp title-caption icon-caption position fps 
            video-driver audio-driver => result
```

Use [RESIZE-WINDOW](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#resize-window) to modify the video surface during program execution.

```
resize-window width height 
              &key bpp flags sw hw fullscreen async-blit any-format 
                   palette double-buffer opengl opengl-attributes 
                   resizable no-frame title-caption icon-caption => result
```

For example;

```
(SDL:WINDOW 320 240 :TITLE-CAPTION "Random Rectangles" :ICON-CAPTION "Random Rectangles"  
                    :DOUBLE-BUFFER T :FULLSCREEN T :POSITION t)
```

**Parameters**

  * `WIDTH` and `HEIGHT` are the pixel width and height of the video surface. If WIDTH and HEIGHT are both 0, then the width and height of the current video mode is used (or the desktop mode, if no mode has been set).
  * `:BPP` is the the number of bits per pixel. See the section [Color depth](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Color_depth) for more information.
  * [:TITLE-CAPTION](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Title_and_Border) appears in the Window title bar.
  * [:ICON-CAPTION](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Title_and_Border) appears when the Window is minimized.
  * [:POSITION](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_x_and_y_position) sets the Window X and Y position.
  * [:FPS](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Frame_Rate) specify game loop to be constant frame rate, or constant time-step.
  * [:VIDEO-DRIVER](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Video_Driver) select the video driver to use,
  * [:AUDIO-DRIVER](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Audio_Driver) select the audio driver to use,
  * [:FULLSCREEN](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Full-screen_or_Windowed), use full-screen mode,
  * [:SW](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Display_Surface_in_System_Memory_or_Video_Memory), and [:HW](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Display_Surface_in_System_Memory_or_Video_Memory), create the surface in System or Video Memory,
  * [:ASYNC-BLIT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Synchronous_or_Asynchronous_updates) update the video surface asynchronously to the main thread,
  * [:ANY-FORMAT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Color_depth) see the Section Color Depth,
  * [:PALETTE](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Exclusive_palette_access) sets exclusive palette access. Without this flag you may not always get the the colors you request using `SDL_SetColors` or `SDL_SetPalette`,
  * [:DOUBLE-BUFFER](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Single_or_Double_buffering) will enable hardware double buffering,
  * [:OPENGL](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#OpenGL_rendering) will create an OpenGL rendering context,
  * [:OPENGL-ATTRIBUTES](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#OpenGL_rendering) will set the OpenGL rendering attributes,
  * [:RESIZABLE](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Title_and_Border) will create a resizable window, and [:NO-FRAME](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Title_and_Border) creates a window with no title bar or frame decoration.

## Dimensions ##

The window width and height are retrieved using [VIDEO-DIMENSIONS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-dimensions).

```
>> (SDL:WINDOW 320 240)

>> (SDL:VIDEO-DIMENSIONS)
#(320 240)
```


## Full-screen or Windowed ##

`LISPBUILDER-SDL` will create a windowed display by default. However this can be changed on startup or any time during program execution by setting `:FULLSCREEN`.

```
>> (SDL:WINDOW 320 240 :FULLSCREEN T)
```

If the specified resolution is not supported in full-screen mode then the next higher resolution will be used and the display window will be centered on a black background.

## Color depth ##

When `:BPP` is unspecified or `0`, the surface will be created with the bits-per-pixel of the current display.

Normally if a video surface of the requested bits-per-pixel is not available one is emulated using a shadow surface. Setting `:ANY-FORMAT` prevents this. If `:ANY-FORMAT` is used and the requested pixel depth (`:BPP`) is unavailable, a display surface having a bits-per-pixel that matches the current display is returned. For example;

```
(SDL:WINDOW 320 240 :BPP 32 :ANY-FORMAT T)
```

The valid values for `:BPP` are as follows;

  * `0`, or `NIL` create the surface using the bits-per-pixel of the current display,
  * `8`, use a 256 index color palette (not supported in lispbuilder-sdl right now),
  * `16`, allows 65,536 colors,
  * `24`, allows 16,777,216 colors,
  * `32`, use the common 8 bits per pixel allowing 4.2 billion colors.

A bits-per-pixel of 24 uses the packed representation of 3 bytes per pixel. For the more common 4 bytes per pixel mode, please use a bits-per-pixel of 32.

## Window Title and Border ##

Use `:TITLE-CAPTION` to set the caption in the window title bar. Use `:ICON-CAPTION` to set the caption when the window is minimized. For example;

```
(SDL:WINDOW 320 240 :TITLE-CAPTION "A Test"
                    :ICON-CAPTION "A Test Minimized"))
```

To allow the window to be resized, set `:RESIZABLE`.  The library will allow the window manager, if any, to resize the window at runtime.  When this occurs, a `VIDEO-RESIZE-EVENT` event is sent to the application, and the application must respond by calling `RESIZE-WINDOW` with the requested size (or another size that suits the application).

```
(SDL:WINDOW 320 240 :TITLE-CAPTION "A Test"
                    :ICON-CAPTION "A Test Minimized")
                    :RESIZABLE T)
```

To remove the title bar and border decoration from the window, set `:NO-FRAME`. Full-screen modes automatically have this flag set.

```
(SDL:WINDOW 320 240 :TITLE-CAPTION "A Test"
                    :ICON-CAPTION "A Test Minimized")
                    :NO-FRAME T)
```

## Window X and Y Position ##

Use `:POSITION` to set the display window x and y position as follows;

  * When `NIL` will use the previous x and y positions,
  * When `T` will center the window on the screen,
  * When `(VECTOR x y)` will set the window to the specified x and y positions.

For example;

```
(SDL:WINDOW 320 240 :POSITION #(100 200))
```

## Display Surface in System Memory or Video Memory ##

The display surface can be created in system memory (`:SW`) or video memory (`:HW`). When the display is created in system memory a software surface is returned. When the display is created in video memory a hardware surface is returned.

A surface in system memory (software surface) improves the performance of pixel level access but is incompatible with some types of hardware blitting.

A surface in video memory (hardware surface) takes advantage of Video-to-Video blits which are often hardware accelerated.

Generally, create your display surface in system memory unless you know what you are doing. This topic is discussed in greater detail in [TBD](TBD)

The system may return a software surface even when a hardware surface is requested as many platforms can only provide a hardware surface when using `SDL-FULL-SCREEN`.

For example;

```
(SDL:WINDOW 320 240 :SW T)
```

## Synchronous or Asynchronous updates ##

`:ASYNC-BLIT` enables the use of asynchronous updates of the display surface. This will usually slow down blitting on single CPU machines, but may provide a speed increase on SMP systems.

An overview is provided on the SDL developers mailing list [here](http://lists.libsdl.org/pipermail/sdl-libsdl.org/2005-February/048955.html), and [here](http://lists.libsdl.org/pipermail/sdl-libsdl.org/2006-January/053805.html)

This has not been well tested on the supported Lisp platforms so use at your own risk. It may be problematic on Lisps that do not support multi-threading.

For example;

```
(SDL:WINDOW 320 240 :ASYNC-BLIT T)
```


## Exclusive palette access ##

`:PALETTE` will guarantee that the colors set by SDL\_SetColors() will be the colors you get. Otherwise, in 8-bit mode, SDL\_SetColors() may not be able to set all of the colors exactly the way they are requested, and you should look at the video surface structure to determine the actual palette.
If SDL cannot guarantee that the colors you request can be set, i.e. if the colormap is shared, then the video surface may be created under emulation in system memory, overriding `:HW`.

For example;

```
(SDL:WINDOW 320 240 :PALETTE T)
```

## Single or Double buffering ##

`:DOUBLE-BUFFER` will try to set up two surfaces in video memory and swap between them when you call `UPDATE-DISPLAY`.  This is usually slower than the normal single-buffering scheme, but prevents "tearing" artifacts caused by modifying video memory while the monitor is refreshing.  Double buffering should only be used by applications that redraw the entire screen on every update.

For example;

```
(SDL:WINDOW 320 240 :DOUBLE-BUFFER T)
```

## OpenGL rendering ##

`:OPENGL` will create an OpenGL rendering context.

For example;

```
(SDL:WINDOW 320 240 :OPENGL T)
```

`:OPENGL-ATTRIBUTES` sets the following attributes for OpenGL;

  * :SDL-GL-RED-SIZE
  * :SDL-GL-GREEN-SIZE
  * :SDL-GL-BLUE-SIZE
  * :SDL-GL-ALPHA-SIZE
  * :SDL-GL-BUFFER-SIZE
  * :SDL-GL-DOUBLEBUFFER
  * :SDL-GL-DEPTH-SIZE
  * :SDL-GL-STENCIL-SIZE
  * :SDL-GL-ACCUM-RED-SIZE
  * :SDL-GL-ACCUM-GREEN-SIZE
  * :SDL-GL-ACCUM-BLUE-SIZE
  * :SDL-GL-ACCUM-ALPHA-SIZE
  * :SDL-GL-STEREO
  * :SDL-GL-MULTISAMPLEBUFFERS
  * :SDL-GL-MULTISAMPLESAMPLES
  * :SDL-GL-ACCELERATED-VISUAL
  * :SDL-GL-SWAP-CONTROL

`:OPENGL-ATTRIBUTES` must be a list of attributes as follows;

```
(SDL:WINDOW 320 240 :OPENGL T
                    :OPENGL-ATTRIBUTES '((:SDL-GL-DOUBLEBUFFER 1)
                                         (:SDL-GL-STEREO 1)))
```


## Redraw the Display ##

Use [UPDATE-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#update-display) to;

  * Update the video surface, or
  * Swap video buffers if double buffering is enabled, or
  * Swap OpenGL buffers if there is a valid OpenGL context, `:OPENGL` is set.

The display should be redrawn once per game loop.

## Clearing the Display ##

Use [CLEAR-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#clear-display) to clear video display using a specific color.

## Determining the Properties of the Current Video Display Surface ##

The following functions return the properties of the current display surface;

  * `OPENGL-CONTEXT-P` returns `T` if there is a valid OpenGL context,
  * `DOUBLE-BUFFERED-P` returns `T` if the display is double-buffered
  * `FULLSCEEN-P` returns `T` if the display is fullscreen, or `NIL` if windowed,
  * `RESIZABLE-P` returns `T` if the display is resizable, or `NIL` if fullscreen or not resizable.
  * [SURFACE-INFO](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#video-info) retrieves the properties of the current display surface.

## Native Window ##

A foreign pointer pointer to the display surface can be retrieved using [GET-NATIVE-WINDOW](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-native-window).

# Application State #

Application state includes mouse focus, keyboard focus and window show state. The following functions will return the current application state;

  * `MOUSE-FOCUS-P`, the application has mouse focus. The mouse is currently within the window area.
  * `INPUT-FOCUS-P`, the application has keyboard focus. All key events will be received by the application.
  * `ACTIVE-P`, the application window show state which can be minimized/iconified, or restored.

For example;

```
>> (SDL:ACTIVE-P)
T
```
embarrassing embarrasing
# Key processing #

`LISPBUILDER-SDL` provides control over Unicode processing and key repeat intervals.

## Unicode ##

To obtain the character codes corresponding to received keyboard events, Unicode translation must first be turned on using `ENABLE-UNICODE`. The translation incurs a slight overhead for each keyboard event and is therefore disabled by default. For each subsequently received `:KEY-DOWN-EVENT`, the `:UNICODE` keyword will contain the corresponding character code, or zero for keys that do not correspond to any character code. _Note_ that only key press events will be translated, not release events.

  * `UNICODE-ENABLED-P`, queries the current state of Unicode keyboard translation. Returns `T` if enabled, and `NIL` if disabled.
  * `ENABLE-UNICODE`, enables Unicode translation.
  * `DISABLE-UNICODE`, disables Unicode translation.

## Key Repeat ##

A single `:KEY-DOWN-EVENT` is generated per key press regardless of how long the key is depressed. When key repeat is enabled, multiple `:KEY-DOWN-EVENT`s are generated per the specified delay and interval.

```
ENABLE-KEY_REPEAT delay interval
```

`DELAY` is the amount of time that elapses before characters repeat when a key is depressed. `INTERVAL` determines how fast characters repeat when a key is depressed. Both `DELAY` and `INTERVAL` are expressed in milliseconds. Setting `DELAY` or `INTERVAL` to `NIL` will set the default values of the constants `SDL-DEFAULT-REPEAT-DELAY` and `SDL-DEFAULT-REPEAT-INTERVAL` respectively. _Note_: `ENABLE-KEY-REPEAT` must be called after the SDL library has been initialized to have any effect.

The functions for controlling key repeat are;

  * `ENABLE-KEY-REPEAT`, enables the keyboard repeat.
  * `DISABLE-KEY-REPEAT`, disables the keyboard repeat.
  * `KEY-REPEAT-ENABLED-P`, returns the current keyboard delay and keyboard repeat rate interval in milliseconds as `(VALUES DELAY INTERVAL)`."
  * `KEY-REPEAT-ENABLED`, returns the current key repeat delay, in milliseconds.
  * `KEY-REPEAT-INTERVAL`, returns the current key repeat interval, in milliseconds.

# The Game Loop #
## Description of the Game Loop ##

Unlike desktop application software that is usually event driven, a game is typically built around a game loop. The game loop is responsible for [processing user input](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Introduction_to_Input_Handling), [executing game logic](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Executing_Game_Logic), [limiting the frame rate](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Setting_the_Frame_Rate_and_Timestep), and [redrawing the display](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Updating_the_Display).

The [WITH-EVENTS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-events) macro forms the core of the game loop and is discussed in the following sections.

## Introduction to Input Handling ##
### Description of Input Events ###

Interaction with the user is a significant part of a game. The game loop receives input from the user, executes the game logic based on this input, and generates output - usually in the form of graphics rendered to a display. For each input stimuli the system will generate a corresponding event. Inputs are received from the mouse, keyboard, joystick, window manager, and user (user generated). The system creates events to match these inputs; for example a key when depressed will generate a key down event. Releasing the key will generate a key up event. The [WITH-EVENTS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-events) macro provides an efficient mechanism for processing these events.

For example, while the mouse is moved a constant stream of `:MOUSE-MOTION-EVENT` events are generated. To process the x and y mouse position the user must provide a `:MOUSE-MOTION-EVENT` handler. `WITH-EVENTS` calls the handler each time this event is received. Event handlers are straight forward to define as shown in the code below;

```
(WITH-EVENTS ()
  (:MOUSE-MOTION-EVENT (:X mouse-x :Y mouse-y)
    ...))
```

The only event that **must** be handled is `:QUIT-EVENT`. If `:QUIT-EVENT` is ignored then it becomes impossible to close the Window or exit the game loop.

```
(WITH-EVENTS ()
  (:QUIT-EVENT () T)
  (:MOUSE-MOTION-EVENT (:X mouse-x :Y mouse-y)
    ...))
```

Event processing is described in greater detail in section [Event Processing](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Event_Processing).

### Description of Input State ###

In addition to event processing, the system also provides functions to query the current state of the keyboard, mouse, display and joystick.

Unlike events that are generated in response to input; state is the way something is with respect to its main attributes. For example a `:KEY-DOWN-EVENT` event may signal that a the `:SDL-KEY-ESCAPE` key is depressed. However the current state of the keyboard may indicate that keys `:SDL-KEY-ESCAPE` and `:SDL-KEY-SPACE` are currently depressed.

Input State is described in greater detail in section [Input State](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Input_State).


## Executing Game Logic ##

`:IDLE`, although not technically an actual event, is executed once per game loop after all events have been processed. Place logic to be executed in the game loop that is not event driven in `:IDLE`. This logic may update world physics, update the state of game objects and render sprites to the display.

```
(SDL:WITH-EVENTS ()
  (:QUIT-EVENT () T)
  (:MOUSE-MOTION-EVENT (:X mouse-x :Y mouse-y)
    ...)
  (:IDLE () 
    (SDL:UPDATE-DISPLAY)))
```

## Setting the Frame Rate ##

[WITH-EVENTS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-events) can limit the game loop to a number of iterations a second, or maintain a constant time-step described in [Frame Rate](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Frame_Rate_and_Timestep).

## Updating the Display ##

Use [UPDATE-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#update-display) to refresh the video surface or swap buffers if double buffering is enabled. The display should be redrawn once per game loop.

# Event Processing #
## Poll or Wait ##

[WITH-EVENTS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-events) can either POLL or WAIT for events on the queue by specifying `:POLL` or `:WAIT` respectively, for example;

```
(SDL:WITH-EVENTS (:POLL)  
  (:QUIT-EVENT () T)  
  (:KEY-DOWN-EVENT (:KEY KEY)  
      (WHEN (SDL:KEY= KEY :SDL-KEY-ESCAPE)  
        (SDL:PUSH-QUIT-EVENT)))  
  (:VIDEO-EXPOSE-EVENT () (SDL:UPDATE-DISPLAY)))))) 
```

If `:POLL`, `WITH-EVENTS` will continuously poll for pending events. If no events are available then the game loop is run and the forms in :IDLE are executed.

If `:WAIT`, `WITH-EVENTS` will sleep indefinitely for the next available event. If no events are available then the game loop is paused. `:IDLE` is ignored when the event mechanism is `:WAIT`.

## Events ##

The following events are supported;

  * [Active/Focus Mouse and Keyboard](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Active_Event),
  * [Key Down/up](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Keyboard_Events),
  * [Mouse Motion](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Mouse_Motion_Event),
  * [Mouse Button Down/Up](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Mouse_Button_Events),
  * [Joystick Axis Motion](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Joystick_Motion_Event),
  * [Joystick Button Down/Up](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Joystick_Button_Events),
  * [Joystick Hat](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Joystick_Hat_Motion_Event),
  * [Joystick Ball](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Joystick_Ball_Motion_Event),
  * [Display Resized](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Resize_Event),
  * [Display Exposed](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Expose_Event),
  * [Window Manager Events](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Window_Manager_Events),
  * [Quit Events](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Quit_Event),
  * [User Defined Events](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#User_Events),
  * [Idle Events](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#IDLE_Event)

### Idle Event ###

```
 (:IDLE ()  
    &BODY BODY) 
```

The `:IDLE` event is special in that it is not a system generated event. The forms in `:IDLE` are executed once per game loop after all events in the event queue are processed. `:IDLE` is ignored when the event mechanism is `:WAIT`.

### Active Event ###

```
(:ACTIVE-EVENT (:GAIN GAIN :STATE STATE)  
   &BODY BODY)
```

`:ACTIVE-EVENT` is generated when the application gains or loses mouse, key or active state. For example an `:ACTIVE-EVENT` event is received when the mouse is moved outside the boundary of the application window.

The following functions return the current state of the application, or interpret the value of `:STATE`.

  * `MOUSE-FOCUS-P` returns `T` if the application has mouse focus - the mouse is within the window area.
  * `INPUT-FOCUS-P` returns `T` if the application has the keyboard focus - keys are captured by the application.
  * `ACTIVE-P` returns `T` if the application is not minimized.

For example;

```
>> (SDL:MOUSE-FOCUS-P state)
T
```

The following functions return the event type, interpreting the value of `:GAIN` and `:STATE` to determine if the application has gained or lost mouse, key or active state.

  * `MOUSE-GAIN-FOCUS-P` if mouse focus was gained or lost, when the mouse leaves or enters the window area
  * `INPUT-GAIN-FOCUS-P` if input focus was gained or lost, the application loses or gains keyboard focus, usually when a different application is made active
  * `ACTIVE-GAIN-P` if the application was minimized/iconified, or restored. `:GAIN` is `T`, when restored and `NIL` minimized/iconified. A single event can have multiple values set in STATE.

For example;

```
>> (SDL:MOUSE-GAIN-FOCUS-P gain state)
T
```

Use :GAIN and :STATE in combination to determine the active event, for example;

```
(:ACTIVE-EVENT (:GAIN gain :STATE state)
   (WHEN (SDL:MOUSE-FOCUS-P state) ;; Mouse focus event
       (IF (SDL:MOUSE-GAIN-FOCUS-P gain state)
            ;; Mouse over window
            )))
```

If the application window has gained state, then `:GAIN` will be `1`, otherwise `:GAIN` will be `0` (zero).

`:STATE` contains a bitmask of any or all of `APP-MOUSE-FOCUS`, `APP-INPUT-FOCUS` or `APP-ACTIVE` identifying the events that caused the change in state.

Note: This event is **not** generated when an application window is first created.

### Keyboard Events ###

```
(:KEY-DOWN-EVENT (:STATE STATE :SCANCODE SCANCODE :KEY KEY :MOD MOD :MOD-KEY MOD-KEY :UNICODE UNICODE)
   &BODY BODY)
```
```
(:KEY-UP-EVENT (:STATE STATE :SCANCODE SCANCODE :KEY KEY :MOD MOD :MOD-KEY MOD-KEY :UNICODE UNICODE)
   &BODY BODY)
```

A keyboard event generally occurs when a key is released or when a key is pressed.
The information on the key that generated the event is stored in `KEY` and `MOD`.

The `SDL-CAPS-LOCK` and `SDL-NUM-LOCK` keys are special cases and report an
`SDL-KEY-DOWN` when first pressed, then an `SDL-RELEASED` when released and pressed again.

The KEYUP and KEYDOWN events are therefore analogous to the state of
> the caps lock and num lock LEDs rather than the keys themselves. These special
> cases are required for compatibility with Sun workstations.

**Note:** Repeating `KEY-DOWN-EVENT` events will occur if key repeat is enabled using
[SDL-ENABLE-KEY-REPEAT](#sdl-enable-key-repeat).

  * `STATE` is `SDL-PRESSED` or `SDL-RELEASED` if the key is pressed or released respectively.
  * `SCANCODE` is the hardware-dependent scancode returned by the keyboard.
  * `KEY` is the value of the key that generated the event. The value for `KEY` generally takes the following format: `:SDL-KEY-0` to `:SDL-KEY-1` for numeric keys. `SDL-KEY-a` to `SDL-KEY-z` for alpha keys in the range a-z.Other keys are generally spelled out, for example `SDL-KEY-PAGEDOWN`, `SDL-KEY-F1` or `SDL-KEY-NUMLOCK` .
  * `MOD` is the keyboard modifier state returned as an `INTEGER`.
  * `MOD-KEY` is the current state of the keyboard modifiers as explained in SDL\_GetModState. Returned as a `LIST` of one or more of `:SDL-KEY-MOD-LSHIFT, :SDL-KEY-MOD-RSHIFT, :SDL-KEY-MOD-LCTRL, :SDL-KEY-MOD-RCTRL, :SDL-KEY-MOD-LALT, :SDL-KEY-MOD-RALT, :SDL-KEY-MOD-LMETA, :SDL-KEY-MOD-RMETA, :SDL-KEY-MOD-NUM, :SDL-KEY-MOD-CAPS, :SDL-KEY-MOD-MODE or :SDL-KEY-MOD-RESERVED`.
  * `UNICODE` is the translated character. The unicode field is only used when UNICODE translation is enabled with SDL\_EnableUNICODE. If unicode is non-zero then this is the UNICODE character corresponding to the keypress. If the high 9 bits of the character are 0, then this maps to the equivalent ASCII character.

### Mouse Motion Event ###

```
(:MOUSE-MOTION-EVENT (:STATE STATE :X X :Y Y :X-REL X-REL :Y-REL Y-REL)  
   &BODY BODY) 
```

A `MOUSE-MOTION-EVENT` event occurs when the mouse moves within the application window or when `SDL-WARP-MOUSE` is called. Both the absolute `X` and `Y` and relative `X-REL` and `Y-REL` coordinates are reported along with the current button state `STATE`. The button state can be interpreted using `SDL-BUTTON`, see `SDL-GET-MOUSE-STATE`.

If the cursor is hidden using `SDL-SHOW-CURSOR` and the input is grabbed using `SDL-WM-GRAB-INPUT`, then the mouse will give relative motion events even when the cursor reaches the edge of the screen. This is currently only implemented on Windows and Linux/Unix-alikes.

  * STATE is the current button state.
  * X is the X coordinates of the mouse
  * Y is the Y coordinates of the mouse
  * X-REL is the relative motion in the X direction
  * Y-REL is the relative motion in the Y direction

### Mouse Button Events ###

```
 (:MOUSE-BUTTON-DOWN-EVENT (:BUTTON BUTTON :STATE STATE :X X :Y Y)  
   &BODY BODY)  
```
```
 (:MOUSE-BUTTON-UP-EVENT (:BUTTON BUTTON :STATE STATE :X X :Y Y)  
   &BODY BODY) 
```

When a mouse button press or release is detected the number of the button pressed (from 1 to 255, with 1 usually being the left button and 2 the right) is placed into BUTTON, the position of the mouse when this event occurred is stored in the X and the Y fields.

Mouse wheel events are reported as buttons 4 (up) and 5 (down). Two events are generated i.e. a MOUSE-BUTTON-DOWN followed by a MOUSE-BUTTON-UP event.

  * BUTTON is the mouse button index which is one of SDL-BUTTON-LEFT, SDL-BUTTON-MIDDLE, SDL-BUTTON-RIGHT, SDL-BUTTON-WHEELUP or SDL-BUTTON-WHEELDOWN.
  * STATE is the state of the button which is SDL-PRESSED or SDL-RELEASED.
  * X is the X coordinates of the mouse at press/release time.
  * Y is the Y coordinates of the mouse at press/release time.

### Joystick Motion Event ###

```
(:JOY-AXIS-MOTION-EVENT (:WHICH WHICH :AXIS AXIS :VALUE VALUE)  
   &BODY BODY) 
```

A JOY-AXIS-MOTION-EVENT event occurs whenever a user moves an axis on the joystick.

  * WHICH is the joystick device index. The index of the joystick that reported the event.
  * AXIS is the joystick axis index
  * VALUE is the current position of the axis (range: -32768 to 32767)

### Joystick Button Events ###

```
(:JOY-BUTTON-DOWN-EVENT (:WHICH WHICH :BUTTON BUTTON :STATE STATE)  
   &BODY BODY)  
```
```
(:JOY-BUTTON-UP-EVENT (:WHICH WHICH :BUTTON BUTTON :STATE STATE)  
   &BODY BODY) 
```

A JOY-BUTTON-DOWN-EVENT or JOY-BUTTON-DOWN-EVENT event occurs whenever a user presses or releases a button on a joystick.

  * WHICH is the index of the joystick that reported the event.
  * BUTTON is the button pressed that caused the event.
  * STATE is the current state of the button and is either SDL-PRESSED or SDL-RELEASED.

### Joystick Hat Motion Event ###

```
   (:JOY-HAT-MOTION-EVENT (:WHICH WHICH :HAT HAT :VALUE VALUE)  
     &BODY BODY) 
```

A JOY-HAT-MOTION-EVENT event occurs when ever a user moves a hat on the joystick.

  * WHICH is the index of the joystick that reported the event.
  * HAT is the index of the hat that generated the event.
  * VALUE is the current position of the hat, a bitwise OR'd combination of the following values SDL-HAT-CENTERED, SDL-HAT-UP, SDL-HAT-RIGHT, SDL-HAT-DOWN, SDL-HAT-LEFT, SDL-HAT-RIGHT-UP, SDL-HAT-RIGHT-DOWN, SDL-HAT-LEFT-UP and SDL-HAT-LEFT-DOWN.

### Joystick Ball Motion Event ###

```
(:JOY-BALL-MOTION-EVENT (:WHICH WHICH :BALL BALL :X-REL X-REL :Y-REL Y-REL)  
  &BODY BODY) 
```

A JOY-BALL-MOTION-EVENT event occurs when a user moves a trackball on the joystick. Trackballs only return relative motion.

  * WHICH is the index of the joystick that reported the event.
  * BALL is the index of the trackball that generated the event.
  * X-REL is the change in X position of the ball since it was last polled (last cycle of the event loop).
  * Y-REL is the change in Y position of the ball since it was last polled (last cycle of the event loop).

### Quit Event ###

```
(:QUIT-EVENT ()  
   &BODY BODY) 
```

If `:QUIT-EVENT` is filtered or ignored then it is impossible for the user to close the window. If `:QUIT-EVENT` is handled and returns `T` then the application window will be closed. If `:QUIT-EVENT` is handled and returns `NIL` then the application window will not be closed. [QUIT-REQUESTED](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#quit-requested) will return non-zero if a `:QUIT-EVENT` is pending.

Note: Screen updates will continue to report success even though the application is no longer visible.

### Window Resize Event ###

```
(:VIDEO-RESIZE-EVENT (:W W :H H)  
   ...) 
```

When `SDL-RESIZABLE` is passed as a flag to WINDOW, the user is allowed to resize the application window. When the window is resized a `VIDEO-RESIZE-EVENT` event is reported, with the new window width and height values stored in `W` and `H` respectively. When an `VIDEO-RESIZE-EVENT` event is received the window should be resized to the new dimensions using WINDOW.

### Video Expose Event ###

```
(:VIDEO-EXPOSE-EVENT ()  
   ...) 
```

A `VIDEO-EXPOSE-EVENT` is generated when the screen has been modified outside of the application, usually by the window manager, and needs to be redrawn. For example the window may be 'Maximized' or 'Restored'.

_Note:_ A `VIDEO-EXPOSE_EVENT` is not generated when the window or screen is initially displayed.

### Window Manager Events ###

```
(:SYS-WM-EVENT ()  
   ...) 
```

The system window manager event contains a pointer to system-specific information about unknown window manager events. If this event is enabled using SDL-EVENT-STATE, it will be generated whenever unhandled events are received from the window manager. This can be used, for example, to implement cut-and-paste in your application. If you want to obtain system-specific information about the window manager, you can fill in the version member of a SDL-SYS-WM-INFO structure using SDL-VERSION, and pass it to the function: SDL-GET-WM-INFO

### User Events ###

```
(:USER-EVENT (:TYPE TYPE :CODE CODE :DATA1 DATA1 :DATA2 DATA2)  
   ...) 
```

USER-EVENT is unique in that it is created by the user. USER-EVENT can be pushed onto the event queue using PUSH-USER-EVENT. The contents of the event are completely up to the programmer.

  * TYPE is a value from SDL-USER-EVENT to (- SDL-NUM-EVENTS 1)` inclusive.
  * CODE is a user defined event code
  * DATA1 is a user defined data pointer
  * DATA2 is a user defined data pointer

### Syntax of WITH-EVENTS ###

```
(WITH-EVENTS (TYPE SDL-EVENT)  
 (:ACTIVE-EVENT (:GAIN GAIN :STATE STATE)  
    ... )  
 (:KEY-DOWN-EVENT (:STATE STATE :SCANCODE SCANCODE :KEY KEY :MOD MOD :UNICODE UNICODE)  
    ... )  
 (:KEY-UP-EVENT (:STATE STATE :SCANCODE SCANCODE :KEY KEY :MOD MOD :UNICODE UNICODE)  
    ...)  
 (:MOUSE-MOTION-EVENT (:STATE STATE :X X :Y Y :X-REL X-REL :Y-REL Y-REL)  
    ...)  
 (:MOUSE-BUTTON-DOWN-EVENT (:BUTTON BUTTON :STATE STATE :X X :Y Y)  
    ...)  
 (:MOUSE-BUTTON-UP-EVENT (:BUTTON BUTTON :STATE STATE :X X :Y Y)  
    ...)  
 (:JOY-AXIS-MOTION-EVENT (:WHICH WHICH :AXIS AXIS :VALUE VALUE)  
    ...)  
 (:JOY-BUTTON-DOWN-EVENT (:WHICH WHICH :BUTTON BUTTON :STATE STATE)  
    ...)  
 (:JOY-BUTTON-UP-EVENT (:WHICH WHICH :BUTTON BUTTON :STATE STATE)  
    ...)  
 (:JOY-HAT-MOTION-EVENT (:WHICH WHICH :HAT HAT :VALUE VALUE)  
    ...)  
 (:JOY-BALL-MOTION-EVENT (:WHICH WHICH :BALL BALL :X-REL X-REL :Y-REL Y-REL)  
    ...)  
 (:VIDEO-RESIZE-EVENT (:W W :H H)  
    ...)  
 (:VIDEO-EXPOSE-EVENT ()  
    ...)  
 (:SYS-WM-EVENT ()  
    ...)  
 (:USER-EVENT (:TYPE TYPE :CODE CODE :DATA1 DATA1 :DATA2 DATA2)  
    ...)  
 (:QUIT-EVENT ()  
    ...  
    T)  
 (:IDLE ()  
    ... )) 
```

It is possible to get hold of the raw event using `*SDL-EVENT*` within the scope of `WITH-EVENTS`.

# Input State #
## Querying the Keyboard State ##

  * [KEY-HELD-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-held-p), returns the duration in seconds that the key has been depressed over a number of game loops.
  * [KEY-PRESSED-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-pressed-p), returns `T` if the key has just been depressed in the current game loop.
  * [KEY-RELEASED-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-released-p), returns `T` if the key has just been released in the current game loop.
  * [KEY-TIME-IN-CURRENT-STATE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-time-in-current-state), returns the duration in seconds that key is either pressed or depressed.
  * [KEY-TIME-IN-PREVIOUS-STATE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-time-in-previous-state), returns the duration in seconds that key was in its previous state, either pressed or depressed.

  * [KEY-DOWN-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#key-down-p), returns `T` if the key is depressed.
  * [KEYS-DOWN-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#keys-down-p), returns a list of the keys that are depressed.
  * [MOD-DOWN-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mod-down-p), returns `T` if the modifier key is depressed.
  * [MODS-DOWN-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mods-down-p), returns a list of the modifier keys that are depressed.

## Querying the Mouse State ##

  * [MOUSE-HELD-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-held-p), returns the duration in seconds that the mouse button has been depressed over a number of game loops.
  * [MOUSE-PRESSED-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-pressed-p), returns `T` if the mouse button has just been depressed in the current game loop.
  * [MOUSE-RELEASED-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-released-p), returns `T` if the mouse button has just been released in the current game loop.
  * [MOUSE-TIME-IN-CURRENT-STATE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-time-in-current-state), returns the duration in seconds that mouse button has been either pressed or depressed.
  * [MOUSE-TIME-IN-PREVIOUS-STATE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-time-in-previous-state), returns the duration in seconds that mouse button was in its previous state, either pressed or depressed.

  * [MOUSE-X](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-x), returns the absolute mouse `X` cursor position.
  * [MOUSE-Y](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-y), returns the absolute mouse `Y` cursor position.
  * [MOUSE-POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-position), returns the absolute mouse `X` and `Y` cursor positions as a `VECTOR`.
  * [MOUSE-RELATIVE-POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-relative-position), returns the relative mouse `X` and `Y` cursor positions as a `VECTOR`. This is the delta between the current mouse position and the position when `MOUSE-RELATIVE-POSITION` was last called.
  * [MOUSE-BUTTONS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-buttons), returns a list of the depressed mouse buttons.
  * [MOUSE-LEFT-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-left-p), returns `T` if the left mouse button is depressed.
  * [MOUSE-MIDDLE-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-middle-p), returns `T` if the middle mouse button is depressed.
  * [MOUSE-RIGHT-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-right-p), returns `T` if the right mouse button is depressed.
  * [MOUSE-WHEEL-UP-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-wheel-up-p), returns `T` if the mouse wheel is rotated up.
  * [MOUSE-WHEEL-DOWN-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-wheel-down-p), returns `T` if the mouse wheel is rotated down.
  * [MOUSE-X1-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-x1-p), returns `T` if the extended mouse button `X1` is depressed.
  * [MOUSE-X2-P](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#mouse-x2-p), returns `T` if the extended mouse button `X2` is depressed.

## Querying the Joystick State ##
[TBD](TBD)

# Frame Rate and Timestep #
## Introduction to Frame Rate and Timestep ##

Simple games can get away with locking the physics simulation to the frame rate. For games that require an accurate physics simulation, the physics simulation must be decoupled from the display frame rate. These two modes are supported by the game loop.

The logic for each mode is encapsulated within a frame rate manager;

  * `FPS-FIXED`, supports a constant or unlocked frame rate where the physics simulation is locked to the frame rate,
  * `FPS-UNLOCKED`, supports an unlocked frame rate where the physics simulation is decoupled from the frame rate, but uses a callback to update the physics simulation.
  * `FPS-TIMESTEP`, same as `FPS-UNLOCKED`, but uses `WITH-TIMESTEP` to update the physics simulation.

The functions used to query and manage frame rate are as follows;

  * `SYSTEM-TICKS`, returns the number of ticks (milliseconds) since system boot,
  * `FRAME-RATE`, query or set the target frame rate. Useful only for `FPS-FIXED` and ignored otherwise,
  * `DT`, returns current time step for the frame and should be used to update the physics simulation,
  * `AVERAGE-FPS`, returns the average frames per second,

Use [FRAME-RATE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#frame-rate) to set the frame rate. For example to set the frame rate to 60 frames per second;

```
(SETF (SDL:FRAME-RATE) 60)
```

To unlock the frame rate, effectively running the game loop as fast as the CPU will allow, set the `FRAME-RATE` to `NIL`;

```
(SETF (SDL:FRAME-RATE) NIL)
```

The delta between frames is retrieved using [DT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#dt)

```
(SDL:DT)
```

The maximum `dt` is retrieved using [MAX-DT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#max-dt).

```
(SDL:MAX-DT)
```


## Constant Frame Rate ##

The game loop may be executed a fixed number of frames per second or as fast as possible. Games that implement a simple physics simulation can get away with locking the physics simulation to to the frame rate and updating the physics simulation once per game loop.
In this mode the value of `DT` will change due to small variations in the duration of each frame. The algorithm used to maintain the frame rate is the same as described in [SDL\_gfx](http://www.ferzkopp.net/joomla/content/view/19/14/).

`:IDLE` is executes each frame and contains game logic, physics and sprite and screen redraws.

The constant frame rate manager, `FPS-FIXED`, must be initialized at display creation time, prior to entering the game loop

```
(SDL:WINDOW 320 240 :FPS (make-instance 'sdl:fps-fixed))
```

`FPS-FIXED` accepts the following optional keyword arguments;

  * `:TARGET-FRAME-RATE` attempts to maintain the specified frame rate,
  * `:WORLD` is used in the calculation of `DT`, where `dt = (/ (- current-frame-ticks previous-frame-ticks) world)`,
  * `:WINDOW` is the number of previous frames over which the average frame rate is calculated,
  * `:MAX-DELTA` is the maximum number of milliseconds between frames.

## Constant Time-step ##

Games that implement a complex physics simulation must use a constant timestep. To maintain a constant timestep the physics simulation is be decoupled from the frame rate. The algorithm used is the same as is described in [Fix Your Timestep!](http://www.gaffer.org/game-physics/fix-your-timestep/).

The physics simulation can be updated via a callback using `FPS-UNLOCKED`, and within `WITH-TIMESTEP` using `FPS-TIMESTEP`.

### Physics Inline ###

`FPS-TIMESTEP` must be initialized prior to entering the game loop, at display creation time.

```
(SDL:WINDOW 320 240 :FPS (make-instance 'sdl:fps-timestep))
```

`FPS-TIMESTEP` accepts the following optional keyword parameters;

  * `:WORLD` is used in the calculation of `DT`, where `dt = (/ (- current-frame-ticks previous-frame-ticks) world)`,
  * `:WINDOW` is the number of previous frames over which the average frame rate is calculated,
  * `:MAX-DT` is the maximum number of milliseconds that the physics simulation can run during a single frame.
  * `:DT` is the timestep (in milliseconds) that is used to update the physics simulation.

Use `:IDLE` to redraw the screen each frame. To update the physics simulation use [WITH-TIMESTEP](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-timestep). `WITH-TIMESTEP` will update the physics simulation as is necessary per frame. This means that the forms within `WITH-TIMESTEP` are executed more often per frame on machines having a lower frame rate.

```
;; Idle work
(:idle
 ;; Clear screen
 (sdl:clear-display sdl:*black*)

 (sdl:with-timestep ()
   ;; Update the particles
   (dolist (p *particles*)
     (update-particle p (/ (sdl::dt) 1000))))

 ;; Update the particles
 (dolist (p *particles*)
   (sdl:draw-surface-at-* *particle-img*
                          (round (vec2-x (particle-pos p)))
                          (round (vec2-y (particle-pos p)))))
 ;; Flip back/front buffers
 (sdl:update-display))
```

### Physics Callback ###

`FPS-UNLOCKED` uses a callback mechanism to update the physics simulation. The callback is passed to `FPS-UNLOCKED` as an optional keyword parameter;

  * `:PS-FN` is the callback function used to update the physics simulation. This callback must be specified as follows.

```
#'(lambda (ticks dt)
    ...)
```

For example;

```
(defun physics (ticks dt)
  (declare (ignore ticks))
  (dolist (p *particles*)
    (update-particle p (/ dt 1000))))

(SDL:WINDOW 320 240 :FPS (make-instance 'sdl:fps-unlocked :ps-fn #'physics))
```

# Default Keyword Arguments #

Many of the functions and macros defined in lispbuilder accept one or more keyword arguments. For example the function [DRAW-PIXEL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-pixel) accepts `:COLOR` and `:SURFACE` keyword arguments that are bound to the symbols [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) and [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) respectively. The objects that are bound to these symbols will be used unless the keyword arguments are overridden by the developer.

The macros [WITH-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surface), and [WITH-SURFACES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surfaces) will bind a surface to [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*DEFAULT-SURFACE*) within the scope of these macros. For example, instead of specifying a target surface for each `draw-*` function a user may bind [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*DEFAULT-SURFACE*) to a surface once and subsequent `draw-*` functions will use this surface while it remains in scope.

```
(DRAW-PIXEL (point :x x1 :y y1) :surface a-surface :color *white*)
(DRAW-PIXEL (point :x x2 :y y2) :surface a-surface :color *white*)	    
(DRAW-PIXEL (point :x x3 :y y3) :surface a-surface :color *white*)
(DRAW-PIXEL (point :x x4 :y y4) :surface a-surface :color *white*)
```

Now, we use [WITH-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surface) to bind a surface to [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*DEFAULT-SURFACE*).

```
(WITH-SURFACE (a-surface)
  (DRAW-PIXEL (point :x x1 :y y1) :color *white*)
  (DRAW-PIXEL (point :x x2 :y y2) :color *white*)
  (DRAW-PIXEL (point :x x3 :y y3) :color *white*)
  (DRAW-PIXEL (point :x x4 :y y4) :color *white*))
```

We can use [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) to bind [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*DEFAULT-COLOR*) to [\*WHITE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*white*):

```
(WITH-SURFACE (a-surface)
  (WITH-COLOR (*white*)
    (DRAW-PIXEL (point :x x1 :y y1))
    (DRAW-PIXEL (point :x x2 :y y2))
    (DRAW-PIXEL (point :x x3 :y y3))
    (DRAW-PIXEL (point :x x4 :y y4)))
```

Finally, if the target surface is the display then [\*WITH-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#WITH-SURFACE) is not required as `:SURFACE` when `NIL` will be bound to [\*DEFAULT-DISPLAY\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*DEFAULT-DISPLAY*) as a convenience. So the above code can be shortened as follows:

```
(WITH-COLOR (*white*)
  (DRAW-PIXEL (point :x x1 :y y1))
  (DRAW-PIXEL (point :x x2 :y y2))
  (DRAW-PIXEL (point :x x3 :y y3))
  (DRAW-PIXEL (point :x x4 :y y4)))
```

But default keyword arguments have an added advantage other than keystroke savings. The macro [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) does not bind a color to the global symbol [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) but rather creates a new local variable called [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*). The local variable in effect _shadows_ the global symbol. Thus [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) is bound to the color black ONLY within the scope of [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color). This means we are able to nest several [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) macros creating in effect a stack. We push a new color onto the stack with each subsequent [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) and the system pops a color from the stack when a [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) goes out of scope. This is illustrated by the following example:

```
(with-surface (*default-display*)
  (with-color (#(0 0 0))
    (with-color (#(255 255 255))
      (draw-rectangle #(10 10 200 200)))
    (draw-rectangle #(40 40 200 100))))
```

This code performs the following hand-waving:

  * [WITH-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surface) binds the global symbol [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) to the display surface (stored in [\*DEFAULT-DISPLAY\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-display*)).
  * Each call to [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) creates a new **local** variable [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) and binds this to the specified color. This local variable [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) is valid only within the scope of [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color).  Each new [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) in effect 'shadows' the previous [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*).
  * [DRAW-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-rectangle) takes as keyword arguments `:COLOR` and `:SURFACE`, where `:COLOR` is bound to the local [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) and `:SURFACE` is bound to [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*).

And thanks to the wonders of Lisp indentation we are able to visualize the color stack directly in the code making debugging a lot easier.

# Optional Packages #

The LISPBUILDER-SDL core functionality can be extended by using one or more of the following packages;

  * LISPBUILDER-SDL will support additional image formats such as PNG and JPG, using [SDL\_image](http://www.libsdl.org/projects/SDL_image/) when the SDL\_image binary is detected.
  * LISPBUILDER-SDL will automatically call functions from  [SDL\_gfx](http://www.ferzkopp.net/joomla/content/view/19/14/) when the SDL\_gfx binary is detected.
  * **lispbuilder-sdl-mixer**: Sound mixing, using [SDL\_mixer](http://www.libsdl.org/projects/SDL_mixer/) a multi-channel audio mixer library
  * **lispbuilder-sdl-ttf**: True-type font rendering, using [SDL\_ttf](http://www.libsdl.org/projects/SDL_ttf/)
  * **lispbuilder-sdl-net**: Networking, using [SDL\_net](http://www.libsdl.org/projects/SDL_net/) a cross-platform, blocking network library.

## SDL\_gfx ##

SDL\_gfx is a native C library with support for many graphics primitives graphics.

See [SDL\_gfx](http://www.ferzkopp.net/joomla/content/view/19/14/) for download & installation instructions.

Alternatively for win32 users, these libraries are included in the `LISPBUILDER-SDL` [binary distribution](http://code.google.com/p/lispbuilder/wiki/WindowsSBCL#Install_LISPBUILDER-SDL_and_Binaries).

## SDL\_image ##

The `SDL_image` library supports the following image formats: TGA, BMP, PNM, PBM, PGM, PPM, XPM, XCF, PCX , GIF, JPG, ICO, CUR, TIF, LBM and PNG. `LISPBUILDER-SDL` will automatically make use of SDL\_image if this library is found at runtime.

See http://www.libsdl.org/projects/SDL_image/ for download & installation instructions.

_Note_ that external libraries must be installed for JPG, PNG and TIFF support:

  * JPG support requires the JPEG library:
    * http://www.ijg.org/
  * PNG support requires the PNG library, and the ZLib library:
    * http://www.libpng.org/pub/png/libpng.html, and
    * http://www.gzip.org/zlib/
  * TIFF support requires the TIFF library:
    * [ftp://ftp.sgi.com/graphics/tiff/](ftp://ftp.sgi.com/graphics/tiff/)

Alternatively for win32 users, these libraries are included in the `LISPBUILDER-SDL` [binary distribution](http://code.google.com/p/lispbuilder/wiki/WindowsSBCL#Install_LISPBUILDER-SDL_and_Binaries).

## LISPBUILDER-SDL-TTF ##

### Overview ###

`LISPBUILDER-SDL-TTF` provides support for the loading and rendering of True-Type fonts.

Functions and symbols exported from the LISPBUILDER-SDL-TTF package are accessible using the `LISPBUILDER-SDL-TTF:` prefix or the shorter form `SDL-TTF:` nickname.

The TrueType library is automatically initialized and uninitialized by the `LISPBUILDER-SDL` package. The functions [INIT-TTF](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#init-ttf) and [QUIT-TTF](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#quit-ttf) are added to the lists [\*EXTERNAL-INIT-ON-STARTUP\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*external-init-on-startup*) and [\*EXTERNAL-QUIT-ON-EXIT\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*external-quit-on-exit*). `LISPBUILDER-SDL` iterates over these lists in calls to [INIT-SDL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#init-sdl), [QUIT-SDL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#quit-sdl) and [WITH-INIT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-init) in order to initialize or uninitialize any external libraries such as `LISPBUILDER-SDL-TTF`.

### Loading LISPBUILDER-SDL-TTF ###

After [installation](http://code.google.com/p/lispbuilder/wiki/DownloadInstallation#lispbuilder-sdl-ttf) is complete, `LISPBUILDER-SDL-TTF` can be loaded by entering the following at the `REPL`;

```
(ASDF:OPERATE 'ASDF:LOAD-OP :LISPBUILDER-SDL-TTF)
```

### Loading LISPBUILDER-SDL-TTF Examples ###

Enter the following at the REPL to load the examples in the `LISPBUILDER-SDL-TTF-EXAMPLES package`;

```
(asdf:operate 'asdf:load-op :lispbuilder-sdl-ttf-examples)
```


The following examples are contained in the package `LISPBUILDER-SDL-TTF-EXAMPLES`:

```
(SDL-TTF-EXAMPLES:FONT-EXAMPLE)
```

### Using LISPBUILDER-SDL-TTF ###

The `LISPBUILDER-SDL-TTF` APIs have the same signature as the `LISPBUILDER-TTF` versions. See the [LISPBUILDER-SDL API Reference](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#contents) for the APIs supported by `LISPBUILDER-SDL-TTF`.




# Primitives #

LISPBUILDER-SDL supports [RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle) and [COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#color) primitives. These are described below.

## Rectangle ##

A new rectangle is created by the function [RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle). The function [RECTANGLE-FROM-EDGES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle-from-edges) will create a new rectangle from the specified top left and bottom right coordinates. The function [RECTANGLE-FROM-MIDPOINT-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle-from-midpoint-*) will create a new rectangle from midpoint.

The macros [WITH-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-rectangle) and [WITH-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-rectangle)s will create a new rectangle and free this rectangle when it goes out of scope.

The rectangle position and width and height can be inspected and modified using the following functions: [X](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#x), [Y](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#y), [WIDTH](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#width) and [HEIGHT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#height). [X2](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#x2) and [Y2](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#y2) will return the `(+ x width)` and `(+ y height)` of the rectangle respectively.

Additional functions that operate on rectangles are as follows:

  * [POINT-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-point-*)
  * [GET-POINT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-point)
  * [SET-POINT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-point)
  * [POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#position)
  * [GET-POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-position)
  * [RECTANGLE-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle-*)
  * [GET-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-rectangle)
  * [SET-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-rectangle)
  * [DRAW-BOX](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-box)
  * [DRAW-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-rectangle)

## Color ##

Use [COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#color) to create a new `RGB` or `RGBA` color.

`RGB/A` color componets can be manipulated using the functions [R](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#r), [G](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#g), [B](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#b), [A](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#a), [SET-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-color) and [SET-COLOR-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-color-*). Compare colors using [COLOR=](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#color=).

Functions that accept a color parameter will most likely bind color to [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) if unspecified. The macro [WITH-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-color) will bind a color to [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) within the scope of this macro. When [\*DEFAULT-COLOR\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-color*) is bound to a color, all subsequent drawing functions will use this implied color while it remains in scope.

Additional functions that operate on colors are as follows:

  * [COLOR-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#color-*)
  * [ANY-COLOR-BUT-THIS](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#any-color-but-this)
  * [MAP-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#map-color)
  * [PACK-COLOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#pack-color)

LISPBULDER-SDL also contains several predefined colors;

  * [\*BLACK\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*black*)
  * [\*WHITE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*white*)
  * [\*RED\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*red*)
  * [\*GREEN\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*green*)
  * [\*BLUE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*blue*)
  * [\*YELLOW\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*yellow*)
  * [\*CYAN\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*cyan*)
  * [\*MAGENTA\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*magenta*)

# Drawing #

LISPBUILDER-SDL provides low-level drawing support for several primitives. Most primitives can be drawn with or without alpha transparency. In addition, the filled primitives can be drawn with a single pixel outline (or stroke) that is a different color to the fill color.

  * Blitting Surfaces: [DRAW-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-surface) and [BLIT-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#blit-surface)
  * Bezier and Cutmull-Rom Curves: [DRAW-BEZIER](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-bezier), [DRAW-SHAPE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-shape), [WITH-BEZIER](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-bezier), [WITH-CURVE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-curve) and [DRAW-CURVE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-curve).
  * Boxes and Rectangles: [DRAW-BOX](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-box), [DRAW-RECTANGLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-rectangle).
  * Circles: [DRAW-CIRCLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-circle) and [DRAW-FILLED-CIRCLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-circle).
  * Lines: [DRAW-HLINE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-hline), [DRAW-VLINE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-vline) and [DRAW-LINE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-line).
  * Points: [DRAW-PIXEL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-pixel).
  * Polygons and Triangles: [DRAW-POLYGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-polygon), [DRAW-TRIGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-trigon) and [WITH-SHAPE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-shape).
  * Filling Surfaces with Color: [FILL-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#fill-surface)
  * Filling Arbitrary Shapes with Color: [FLOOD-FILL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#flood-fill)

# Displaying Text #

`LISPBUILDER-SDL` can render text over a transparent background (Solid rendering), render text over a colored background (Shaded rendering), and anti-alias text into a transparent background (blended text). Text may be left, right or center justified. Text can be rendered using bitmaps fonts or Truetype fonts.

The following table described the font capabilities of `LISPBUILDER-SDL`;

| **Package** | **"Simple" Fonts** | **Bitmap Fonts** | **Truetype Fonts** |
|:------------|:-------------------|:-----------------|:-------------------|
| `LISPBUILDER-SDL` | Yes                | Yes              | Yes, using `VECTO` |
| `SDL_gfx`   | -                  | Yes              | -                  |
| `LISPBUILDER-SDL-TTF` | -                  | -                | Yes                |

Shown above, `LISPBUILDER-SDL` is able to both render bitmap and Truetype fonts. The advantage when using only `LISPBUILDER-SDL` and optionally `VECTO` is that additional external foreign libraries are not needed. The advantage when SDL\_gfx is detected or `LISPBUILDER-SDL-TTF` is used is speed.

`LISPBUILDER-SDL` and `LISPBUILDER-SDL-TTF` use the same text and font API function calls. `VECTO` uses its own API as described [here](http://www.xach.com/lisp/vecto/#sect-text).

## Fonts ##

Initializing fonts;

  * [INITIALISE-FONT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Font_Initialization) initializes a font for use from the specified font definition.
  * [INITIALISE-DEFAULT-FONT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Font_Initialization) initializes a font for use from the specified font definition. Uses the `*font-8x8*` font definition if unspecified.

Setting the font typeface;

  * [SET-FONT-STYLE](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Font_Style) sets the font Typeface of normal, bold, italic or underline.

Drawing text to a surface;

  * [DRAW-STRING-SOLID](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) draw text with a transparent background
  * [DRAW-STRING-SOLID-\*](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text)
  * [DRAW-STRING-SHADED](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) draw text with a colored background
  * [DRAW-STRING-SHADED](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text)
  * [DRAW-STRING-BLENDED](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) draw anti-aliased text with a transparent background
  * [DRAW-STRING-BLENDED-\*](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text)

Draw text to a new surface;

  * [RENDER-STRING-SOLID](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) returns a new surface containing the text and having a transparent background sized to the text width and height
  * [RENDER-STRING-SHADED](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) returns a new surface containing the text having a colored background sized to the text width and height
  * [RENDER-STRING-BLENDED](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) returns a new surface containing the anti-aliased text having a transparent background sized to the text width and height

Blit a cached surface to the target surface;

  * [DRAW-FONT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text) blits the cached `SURFACE` in the font to the destination `SURFACE`. The cached surface is created during a previous call to any of the `RENDER-STRING*` functions.
  * [DRAW-FONT-AT](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text)
  * [DRAW-FONT-AT-\*](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Rendering_Text)

Querying font attributes;

  * `X`, `Y`, `WIDTH`, `HEIGHT` return the position and dimensions of the cached font surface
  * `get-Glyph-Metric`
  * `get-Font-Size`
  * `set-Font-style`
  * `get-Font-style`
  * `get-font-height`
  * `get-font-ascent`
  * `get-font-descent`
  * `get-font-line-skip`
  * `get-font-faces`
  * `get-font-face-family-name`
  * `get-font-face-style-name`
  * `font-fixed-width-p`
  * `CHAR-WIDTH`
  * `CHAR-HEIGHT`
  * `CHAR-PITCH`
  * `CHAR-SIZE`

Freeing a font;

  * `free-cached-surface`
  * `FREE`

Font macros;

  * `WITH-DEFAULT-FONT`
  * `WITH-FONT`

### Bitmapped Fonts ###

`LISPBUILDER-SDL` supports two kinds of bitmap fonts; a "simple" font where the font data is created from a bitmapped image loaded from the drive, and a bitmap font having the font data used in `SDL_gfx`.

The "simple" font is available using the `*simple-font-4x5*` font definition. The font looks as follows;

![https://lispbuilder.github.io/documentation/font.jpg](https://lispbuilder.github.io/documentation/font.jpg)

Bitmaps fonts support the SDL\_gfx font format. The following bitmap font definitions are created at compile time and are always accessible in the `LISPBUILDER-SDL` library; `*FONT-10X20*`, `*FONT-5X7*`, `*FONT-5X8*`, `*FONT-6X10*`, `*FONT-6X12*`, `*FONT-6X13*`, `*FONT-6X13B*`, `*FONT-6X13O*`, `*FONT-6X9*`, `*FONT-7X13*`, `*FONT-7X13B*`, `*FONT-7X13O*`, `*FONT-7X14*`, `*FONT-7X14B*`, `*FONT-8X13*`, `*FONT-8X13B*`, `*FONT-8X13O*`, `*FONT-8X8*`, `*FONT-9X15*`, `*FONT-9X15B*`, `*FONT-9X18*` and `*FONT-9X18B*`.

### Truetype Fonts ###

Support for Truetype fonts is provided by [LISPBUILDER-SDL-TTF](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#LISPBUILDER-SDL-TTF) or [VECTO](http://www.xach.com/lisp/vecto/).

The [VECTO](http://www.xach.com/lisp/vecto/#sect-text) documentation describes how to render text using this package. Once rendered, text is copied from a VECTO buffer into a `LISPBUILDER-SDL` `SURFACE` using `VECTO->SURFACE`.

## Font Attributes ##

### Glyph Metrics ###

```
```

`GET-GLYPH-METRIC` returns the glyph metrics for the character `CH`, or `NIL` upon error.

  * `CH` is a Unicode character specified as an `INTEGER`.
  * `FONT` is the `FONT` object from which to retrieve the glyph metrics of the character `CH`. Binds to `*DEFAULT-FONT*` by default.
  * `METRIC` is a `KEY`word argument and may be one of; `:MINX` for the minimum `X` offset, `:MAXX` for the maximum `X` offset, `:MINY` for the minimum `Y` offset, `:MAXY` for the maximum `Y` offset, `:ADVANCE` for the advance offset.

For example;

```
(SDL:GET-GLYPH-METRIC char :METRIC :MINX
```

### Font Size ###

```
```

`GET-FONT-SIZE` calculates and returns the resulting `SIZE` of the `SURFACE` that is required to render the `TEXT`, or `NIL` on error. No actual rendering is performed, however correct kerning is calculated for the actual width. The height returned is the same as returned using `GET-FONT-HEIGHT`.

  * `TEXT` is the string to size, of type `STRING`.
  * `SIZE` must be one of; `:W` for the text width or `:H` for the text height.
  * `FONT` is the font used to calculate the size of the `TEXT`. Binds to `*DEFAULT-FONT*` by default.

### Font Style ###

```
```

`GET-FONT-STYLE` returns the rendering style of the `FONT`. If no style is set then `:STYLE-NORMAL` is returned, or `NIL` upon error.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the font style as one or more of: `:STYLE-NORMAL`, `:STYLE-BOLD`, `:STYLE-ITALIC`, `:STYLE-UNDERLINE`

`SET-FONT-STYLE` sets the rendering `STYLE` of `FONT`. This will flush the internal cache of previously rendered glyphs, even if there is no change in style, so it may be best to check the current style using `GET-FONT-STYLE` before setting the style.

  * `STYLE` is a list of one or more: `:STYLE-NORMAL`, `:STYLE-BOLD`, `:STYLE-ITALIC`, `:STYLE-UNDERLINE`.

_Node_: Combining `:STYLE-UNDERLINE` with anything can cause a **segfault**, other combinations may also do this.

### Font Height ###

```
```

`GET-FONT-HEIGHT` returns the maximum pixel height of all glyphs of `FONT`.
Use this height for rendering text as close together vertically as possible,
though adding at least one pixel height to it will space it so they can't touch.
Remember that `LISPBUILDER-SDL` doesn't handle multi-line printing so you are responsible
for line spacing, see `GET-FONT-LINE-SKIP` as well.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the height of the `FONT` as an `INTEGER`.

### Font Ascent ###

```
```

`GET-FONT-ASCENT` returns the maximum pixel ascent of all glyphs of `FONT`.
This can also be interpreted as the distance from the top of the font to the baseline.
It could be used when drawing an individual glyph relative to a top point, by combining it with the glyph's `:MAXY` metric to resolve the top of the rectangle used when blitting the glyph on the screen.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the ascent of the `FONT` as an `INTEGER`.

### Font Descent ###

```
```

`GET-FONT-DESCENT` returns the maximum pixel descent of all glyphs of `FONT`.
This can also be interpreted as the distance from the baseline to the bottom of the font.
It could be used when drawing an individual glyph relative to a bottom point, by combining it with the glyph's `:MAXY` metric to resolve the top of the rectangle used when blitting the glyph on the screen.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the descent of the `FONT` as an `INTEGER`.

### Font Line Skip ###

```
```

`GET-FONT-LINE-SKIP` returns the recommended pixel height of a rendered line of text of the `FONT`. This is usually larger than the `GET-FONT-HEIGHT` of the font.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the pixel height of the `FONT` as an `INTEGER`.

### Font Faces ###

```
```

`GET-FONT-FACES` returns the number of faces 'sub-fonts' available in the `FONT`.
This is a count of the number of specific fonts (based on size and style and other typographical features perhaps) contained in the font itself. It seems to be a useless
> fact to know, since it cannot be applied in any other font functions.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the number of faces in the `FONT` as an `INTEGER`.

### Font Fixed Width ###

```
```

'FONT-FIXED-WIDTH-P` returns `T` if the font face is of a fixed width, or `NIL` otherwise. Fixed width fonts are mono-space, meaning every character that exists in the font is the same width.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns `T` if `FONT` is of fixed width, and `NIL` otherwise.

### Font Face Family Name ###

```
```

`GET-FONT-FACE-FAMILY-NAME` returns the current font face family name of `FONT` or `NIL` if the information is unavailable.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the name of the `FONT` face family name as a `STRING`, or `NIL` if unavailable.

### Font Face Style Name ###

```
```

`GET-FONT-FACE-STYLE-NAME` returns the current font face style name of `FONT`, or `NIL` if the information is unavailable.

  * `FONT` is a `FONT` object. Binds to `*DEFAULT-FONT*` by default.
  * Returns the name of the `FONT` face style as a `STRING`, or `NIL` if unavailable.

### Free Cached Surface ###

```
```

`FREE-CACHED-SURFACE` will free the `FONT` cached surface created in a `RENDER-STRING` call.

### Free Font ###

```
```

`FREE` will all resources allocated for the `FONT` including any cached surface.

## Font Initialization ##

A font must be initialized prior to use using [INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font), or [INITIALISE-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-font). These functions accept a [font definition](http://code.google.com/p/lispbuilder/wiki/UsingLispbuilderSDL#Font_Definition) and return a font object or `NIL` if the font cannot be initialized. [INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font) will use `*font-8x8*` if a definition is unspecified. [INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font) binds the new font to `*DEFAULT-FONT*` to be used for subsequent font rendering or drawing operations.

```
initialise-font font-definition => result
```

Resources allocated to a font are freed by [FREE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#free).

## Font Definition ##

[INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font) and [INITIALISE-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-font) accept a font definition and return a font object. A font definition is specific to the type of font.

To load a Truetype font from disk, first create a Truetype font definition as follows;

```
(make-instance 'SDL:ttf-font-definition
               :size 32
               :filename (merge-pathnames "Vera.ttf" *default-font-path*))
```

Then pass this font definition to [INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font), or [INITIALISE-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-font) to create the font object.

```
(defparameter *vera-ttf* (make-instance 'SDL:ttf-font-definition
                                        :size 32
                                        :filename (merge-pathnames "Vera.ttf" *default-font-path*))

(unless (SDL:INITIALIZE-DEFAULT-FONT *vera-ttf*)
   (error "Cannot create font."))
```

To create a bitmap font definition, where `:DATA` is the same format of the fonts used in `SDL_gfx`;

```
(make-instance 'SDL:bitmap-font-definition
               :width 6
               :height 9
               :data #(...)))
```

To create a simple font definition;

```
(make-instance 'sdl:simple-font-definition
                 :width 4 :height 5
                 :character-map "abcdefghijklmnopqrstuvwxyz:'!?_-,.()#~0123456789"
                 :character-mask (loop for y from 0 below 1
                                       append (loop for x from 0 below 48
                                                    collect (list (* x 4) (* y 5) 4 5)))
                 :color-key (sdl:color :r 99 :g 0 :b 0)
                 :filename (sdl:create-path "font.bmp" sdl:*default-asset-path*))
```

## Rendering Text ##

LISPBUILDER-SDL supports the rendering of colored text over a transparent background (Solid rendering), and the rendering of colored text over a solid colored background (Shaded rendering). Text may be left, right or center justified. Text is drawn to a target surface using the `DRAW-STRING` functions, or rendered to a new surface of character height and string width using the `RENDER-STRING` functions. the `RENDER-STRING` functions can optionally cache the new surface in the font object. A cached surface can be accessed using [CACHED-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#cached-surface)) and can be blitted to a target surface using [DRAW-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-font).

The following functions draw text directly to a surface;

  * `draw-string-solid`
  * `draw-string-solid-*`
  * `draw-string-shaded`
  * `draw-string-shaded-*`
  * `draw-string-blended`
  * `draw-string-blended-*`

The following functions will draw text to a new surface, and optionally cache that surface in the `FONT` object;

  * `render-string-solid`
  * `render-string-shaded`
  * `render-string-blended`
  * `CACHED-SURFACE`

The following functions will blit the cached surface to the target surface;

  * `DRAW-FONT-AT`
  * `DRAW-FONT-AT-*`

The following methods also apply to fonts; [X](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#x), [Y](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#y), [WIDTH](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#width), [HEIGHT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#height), returns the width of the cached [SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#surface).

The font rendering functions accept a font object which is bound to [\*DEFAULT-FONT\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-font*) when unspecified. The function [INITIALISE-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#initialise-default-font) and the macros [WITH-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-font) and [WITH-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-default-font) will bind a font to [\*DEFAULT-FONT\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-font*). All font drawing or font rendering functions that take a &KEYword or &OPTIONAL `:FONT` argument will use the font assigned to [\*DEFAULT-FONT\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-font*) unless another font is specified. This makes calling these functions a bit easier as the `:FONT` parameter need not be explicitly passed. For example:

```
(WITH-COLOR (*WHITE*)
  (WITH-FONT (*FONT-8x8*)
    (DRAW-STRING-SOLID-* "Font is 8x8." 10 10)

    (WITH-FONT (*FONT-10x20*)
      (DRAW-STRING-SOLID-* "Font is 10x20." 10 20))

    (DRAW-STRING-SOLID-* "Font is 8x8 again." 10 40)))
```

Note the difference between [WITH-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-font) and [WITH-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-default-font).

  * [WITH-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-font) takes a font definition and will initialize and assign the font to `*DEFAULT-FONT` within the scope of the macro. The font will be closed when out of scope of the macro.
  * [WITH-DEFAULT-FONT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-default-font) takes an already initialized font object and assigns the font to `*DEFAULT-FONT` within the scope of the macro.

### Rendering Solid Text ###

```
draw-string-solid string p1 &key justify surface font color => result
draw-string-solid-* string x y &key justify surface font color => result
```

Renders the `STRING` using `FONT` with text `COLOR` to the target `SURFACE`. The surface background is transparent and therefore can be keyed over other surfaces.

Use `:CACHE T` to cache the new surface in the `FONT` object.
When `:FREE T` any existing cached surface in `FONT` is automatically freed.
When `:FREE NIL` the caller is responsible for freeing any existing cached surface in `FONT`.

Any cached surface can be accessed using `CACHED-SURFACE` and can be blitted to a target surface using `DRAW-FONT`.

For example;

```
(SDL:DRAW-STRING-SOLID "Hello World!" :COLOR SDL:*WHITE*)
```

Renders the `STRING` using `FONT` with text `COLOR` to a new `SURFACE`. The surface background is transparent and therefore can be keyed over other surfaces. The dimensions of the new surface are `FONT` height and width x `STRING` length. The surface background is transparent and can be keyed over other surfaces.

```
render-string-solid string &key font color free cache => result
```

### Shaded ###

```
draw-string-shaded string p1 fg-color bg-color &key justify surface font => result
draw-string-shaded-* string x y fg-color bg-color &key justify surface font => result
```

Draw `STRING` using `FONT` with foreground color `FG-COLOR` and background color `BG-COLOR` to the target `SURFACE`.  `FONT` is bound to `*DEFAULT-FONT*` if unspecified.

Use `:CACHE T` to cache the new surface in the `FONT` object.
When `:FREE T` any existing cached surface in `FONT` is automatically freed.

For example;

```
(SDL:DRAW-STRING-SHADED "Hello World!" SDL:*WHITE* SDL:*BLUE*)
```


```
render-string-shaded string fg-color bg-color &key font free cache => result
```

Draw `STRING` using `FONT` with foreground color `FG-COLOR` and background color `BG-COLOR` to a new `SURFACE`.  `FONT` is bound to `*DEFAULT-FONT*` if unspecified. The dimensions of the new surface are `FONT` height and width x `STRING` length. The surface background is transparent and can be keyed over other surfaces.

### Blended ###

```
draw-string-blended string p1 &key justify surface font color => result
draw-string-blended-* string x y &key justify surface font color => result
```

Draw the anti-aliased `STRING` using `FONT` with text `COLOR` to the target `SURFACE`.  `FONT` is bound to `*DEFAULT-FONT*` if unspecified. The surface background is transparent and can be keyed over other surfaces.

Use `:CACHE T` to cache the new surface in the `FONT` object.
When `:FREE T` any existing cached surface in `FONT` is automatically freed.
When `:FREE NIL` the caller is responsible for freeing any existing cached surface in `FONT`.

Any cached surface can be accessed using `CACHED-SURFACE` and can be blitted to a target surface using `DRAW-FONT`.

For example;

```
(SDL:DRAW-STRING-BLENDED "Hello World!" :COLOR SDL:*WHITE*)
```

Draw the anti-aliased `STRING` using `FONT` with text `COLOR` to the target `SURFACE`. The dimensions of the new surface are `FONT` height and width x `STRING` length. The surface background is transparent and can be keyed over other surfaces.


# Surfaces #

## Overview of Surfaces ##

An SDL surface, [SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#surface), represents an area of _graphical_ memory that can be drawn or rendered to, for example the video framebuffer or an image that is loaded from disk.

## Creating Surfaces ##

A surface is created:

  * Automatically by the SDL library to represent a framebuffer by calling [WINDOW](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#window).
  * When loading an image from disk using [LOAD-IMAGE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#load-image).
  * Explicitely using [CREATE-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#create-surface), or [COPY-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#copy-surface).
  * Implicitely using [CONVERT-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#convert-surface).

Functions that accept a surface parameter will most likely bind surface to [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) when unspecified. The macros [WITH-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surface) and [WITH-SURFACES](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#with-surfaces) will bind a [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) within the scope of these macro. [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) is bound to a surface, all subsequent drawing functions will use this implied surface while it remains in scope.

A surface can be explicitely freed by calling [FREE-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#free-surface).

## Video Memory Or System Memory ##

A surface in system memory (software surface) improves the performance of pixel level access but is incompatible with some types of hardware blitting.

A surface in video memory (hardware surface) takes advantage of Video-to-Video blits which are often hardware accelerated.

A hardware surface (`SDL-HW-SURFACE`) must be locked prior to performing per-pixel manipulations. When locked the hardware surface is copied from video memory to system memory and copied back when unlocked. This can cause a **major** performance hit.

Use software surfaces (`SDL-SW-SURFACE`) to perform per-pixel manipulations, or to blit surfaces having alpha channels at a high frame rate when alpha blitting is not supported in hardware.

Use hardware surfaces when per-pixel manipulations are not required, or when the graphics hardware supports blitting of surfaces having alpha channels, or when the surfaces can be stored in video memory.

Many of the drawing functions require per-pixel manipulation of the destination surface. If drawing directly to the display surface then the display surface must be a software surface (in system memory) or performance will be effected.
Drawing functions that require per-pixel manipulation of a destination surface include; [DRAW-BEZIER](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-bezier), [DRAW-CURVE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-curve), [DRAW-SHAPE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-shape), [DRAW-LINE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-line), [DRAW-PIXEL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-pixel), [READ-PIXEL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#read-pixel), [DRAW-FILLED-CIRCLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-circle,), [DRAW-CIRCLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-circle,), [DRAW-FILLED-CIRCLE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-circle,), [DRAW-TRIGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-trigon,), [DRAW-POLYGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-polygon,), [DRAW-ELLIPSE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-ellipse,), [DRAW-FILLED-ELLIPSE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-ellipse,), [DRAW-PIE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-pie,), [DRAW-FILLED-PIE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-pie,), [DRAW-FILLED-TRIGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-trigon,), and [DRAW-FILLED-POLYGON](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-filled-polygon,).

To efficiently use the drawing functions with hardware surfaces, render to a software surface and then blit the software surface to a hardware surface using [BLIT-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#blit-surface).

## Images ##

Images are loaded from disk using [LOAD-IMAGE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#load-image). `LISPBUILDER-SDL` will attempt to detect the image type and load an image using the Magic Number in the image file. For image formats that have no magic number such as targa (.TGA), [LOAD-IMAGE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#load-image) allows the image type to be specified as a parameter.

A surface is saved to disk as a BMP image using [SAVE-IMAGE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#save-image)

```
(DRAW-SURFACE (LOAD-IMAGE "sdl.bmp") 
              :surface *default-display*)
```

`SUPPORTED-IMAGE-FORMATS` returns a list of the supported image formats. `LISPBUILDER-SDL` supports only the BMP format when `SDL_image` is not available at runtime. Support for additional image formats is provided by `SDL_image`.

```
(SUPPORTED-IMAGE-FORMATS)
>> (:BMP :PNM :PPM :PGM :PBM :XPM :LBM :PCX :GIF :TGA :XV :XCF :ICO :CUR :JPG :PNG :TIF)
```

`*IMAGE-LOADED-P*` will be `T` if `SDL_image` has been loaded at runtime.

```
*IMAGE-LOADED-P*
>> `T`
```

`INIT-IMAGE` will preload the JPG, PNG or TIF libraries. These libraries are usually loaded on demand within `LOAD-IMAGE` and unloaded thereafter. If there is a concern about loading/unloading these libraries each time an image is loaded, then `INIT-IMAGE` can be used to load the libraries once. Use `QUIT-IMAGE` to unload these libraries from memory.

`INIT-IMAGE` will return the a list of the libraries that were loaded.

```
(IMAGE-INIT :JPG :TIF :PNG)
>> (:JPG :PNG :TIF)
```

`IMAGE-INIT-P` accepts a list of image formats and returns these formats if loaded.

```
(IMAGE-INIT :JPG :TIF :PNG)
>> (:JPG :PNG)
```

`IMAGE-P` returns `T` if the image is of the specified format, or NIL otherwise.

```
(IMAGE-P *image* :BMP)
>> :BMP
```

`IMAGE-TYPE-OF` will return the image format.

```
(IMAGE-P *image*)
>> :BMP
```

`IMAGE-SUPPORTED` will return `T` if the image is of a format that can be loaded by `LISPBUILDER-SDL`.

```
(IMAGE-SUPPORTED-P *image*)
>> :BMP
```


## Positioning Surfaces ##

A [SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#surface) stores its own position X/Y coordinates. These coordinates can be inspected and modified using the following functions: [X](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#x), [Y](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#y), [WIDTH](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#width) and [HEIGHT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#height).

Additional functions and macros that manage surface coordinates are as follows:

  * [POINT-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#point-*)
  * [GET-POINT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-point)
  * [SET-POINT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-point)
  * [SET-POINT-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-point-*)
  * [POSITION-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#position-*)
  * [GET-POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-position)
  * [GET-POSITION](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-position-*)
  * [SET-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-surface)
  * [SET-SURFACE-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-surface-*)
  * [RECTANGLE-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#rectangle-*)
  * [GET-RECTANGLE-\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-rectangle-*)
  * [GET-SURFACE-RECT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-surface-rect)
  * [DRAW-SURFACE-AT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-surface-at)

## Drawing & Blitting Surfaces ##
The functions [BLIT-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#blit-surface) and [DRAW-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-surface) will blit or draw a source surface onto a destination surface using the position coordinates stored in the source surface.[DRAW-SURFACE-AT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#draw-surface-at) will draw the source surface at a specified position.

The composition rules that determine how the source surface is composed over the destination surface are described in the description of [BLIT-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#blit-surface). [FILL-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#fill-surface) fill will the target surface with a single color.
> <a href='#devprim'>Drawing Primitives</a> describes how [\*DEFAULT-SURFACE\*](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#*default-surface*) is used when calling blitting operations.

Use [SET-COLOR-KEY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-color-key), [CLEAR-COLOR-KEY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#clear-color-key) and [SET-ALPHA](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-alpha) to modify the key color and alpha transparency properties of a surface after the surface has been created.

## Clipping ##

Set a _clipping_ rectangle to limit the draw area on a destination surface. The clipping rectangle for a surface is manipulated using [GET-CLIP-RECT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#get-clip-rect) and [SET-CLIP-RECT](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-clip-rect). When this surface is the destination of a blit, only the area within the <em>clip</em> rectangle will be drawn into.

## Sprite Sheets ##

`LISPBUILDER-SDL` can render a single frame from a sprite sheet. A sprite sheet is a single image comprised of multiple frames of animation.  By assigning a sequence of _cell_ rectangles to a sprite sheet, only the current frame will be drawn to the display at any one time. When a sprite sheet is the source of a blit, only the areas within the <em>cell</em> rectangle will be drawn. Thus cells are used when only a portion of the source surface must be blitted to a destination surface.

https://lispbuilder.github.io/documentation/lispbuilder-sdl/assets/ani2.bmp

After the sprite sheet is loaded using `LOAD-IMAGE`, the position and width height of each animation frame can be assigned using `CELLS`. A single frame from the sheet is rendered by giving the `:CELL` index to `DRAW-SURFACE`.

```
;; Load the sprite sheet
(defparameter *sh* (sdl:load-image "ani2.bmp"))

;; Create the cells.
;; Each 'cell' is the x/y width/height of an image in the sprite sheet
(defparameter *cells* (loop for y from 0 to 192 by 64
                            append (loop for x from 0 to 192 by 64
                                         collect (list x y 64 64))))
;; Assign the cells to the sprite-sheet
(setf (sdl:cells *sh*) *cells*)

;; Draw a frame of animation
(sdl:draw-surface-at *sh* #(10 10) :cell 0)
```

The example `(SDL-EXAMPLES:EXPLOSION)` uses cells to animate an explosion.

The cell rectangle for a surface is manipulated using [CLEAR-CELL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#clear-cell) and [SET-CELL](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#set-cell).

## Alpha Blending and Color Keying ##

`LISPBUILDER-SDL` supports per-pixel alpha blending, per-surface alpha blending and color keying.

  * Per-pixel alpha will blend a source surface to a destination surface using the alpha channel of the source surface.
  * Per-surface alpha will blend a source surface to a destination surface using the alpha value of the source surface.
  * Color keying copies only those pixels that do not match the color key from the source surface to the destination surface.

A surface contains red, green, blue (RGB), and optional alpha (RGBA) component, a surface alpha value, and an optional color key.

A surface must have per-surface alpha enabled for per-surface alpha blending to be perfomed. Per-surface alpha is enabled and set using `:ALPHA` at time of surface creation, or enabled or disabled using `ALPHA-ENABLED-P` for existing surfaces. The per-surface alpha value is set using `:ALPHA` at time of surface creation, or `ALPHA` for existing surfaces.

A surface must have both an alpha component (RGBA) and per-surface alpha must be enabled for per-pixel alpha blending to be performed. The alpha channel can only be added to a surface at time of surface creation, using `:PIXEL-ALPHA`. A RGB surface can be copied to a new RGBA surface using `CONVERT-SURFACE`, or `CONVERT-TO-DISPLAY-FORMAT`.

A surface must have color keying enabled for color keying to be perfomed. The color key is enabled and set using `:COLOR-KEY` at time of surface creation, or enabled or disabled using `COLOR-KEY-ENABLED-P` for existing surfaces. The color key is set using `COLOR-KEY` for existing surfaces.

Per-pixel and per-surface alpha blending are mutually exclusive with per-pixel alpha having priority over per-surface alpha, as described above and show in the table below. Per-surface alpha blending is only performed when the source source does not have an alpha channel (RGB). If the source surface has an alpha channel (RGBA) and per-surface alpha is enabled then per-pixel alpha is performed.

The display will typically be an RGBA surface, so blitting a surface to the display will follow the rules defined in #3, #4 and #5 in the table below.

The alpha channel and per-surface alpha of the **source** surface and not the destination surface are used when performing alpha blending.

The following table summaries how per-pixel, per surface and color key settings are used during blitting;

| | **Source**         | **Destination** | **Per-pixel Alpha** | **Per-surface Alpha** | **Color Keying** |
|:|:-------------------|:----------------|:--------------------|:----------------------|:-----------------|
|1| RGB(A) + :ALPHA    | RGB             | Performed           | Ignored               | Ignored          |
|2| RGB(A)             | RGB             | No                  | No                    | Yes, if set      |
|3| RGB    + :ALPHA    | RGB(A)          | No                  | Performed             | Yes, if set      |
|4| RGB(A) + :ALPHA    | RGB(A)          | Performed           | Ignored               | Ignored          |
|5| RGB(A)             | RGB(A)          | No                  | No                    | Yes, if set      |
|6| RGB    + :ALPHA    | RGB             | No                  | Performed             | Yes, if set      |
|6| RGB                | RGB             | No                  | No                    | Yes, if set      |

  * #1 Is the alpha channel in destination left untouched?
  * #2 RGB data copied from source.
  * #3 Alpha component of copied pixels set to opaque.
  * #4 Alpha blending is performed, but the alpha channel in the destination surface left untouched. This means that you cannot compose two arbitrary RGBA surfaces this way and get the result you would expect from "overlaying" them; the destination alpha will work as a mask.
  * #5 RGBA data copied from source.

Alpha blitting can be accelerated using `:RLE-ACCEL`.

NOTE: To load the alpha channel of an image, make ensure that `SDL_image` is installed and available at runtime using SDL:**IMAGE-LOADED-P**. A workaround used in `SDL-EXAMPLES:PARTICLES` and show below is to use an image processor to save the alpha channel of the image as the red channel of a new 'alpha-image'. Then overwrite the (A) alpha channel of the image using the (R) channel of the 'alpha-image'.

```
    ;; Load the main image, converting the RGB surface into an RGBA surface.
    (setf *particle-img* (sdl::convert-to-display-format :surface (sdl:load-image "particle.bmp"))
                                                         :inherit nil
                                                         :pixel-alpha t
                                                         :free t))

    ;; Then replace the alpha channel of *particle-img* with
    ;; the Red channel that contains the alpha channel pixel data
    (sdl:with-surface (alpha (sdl:load-image "particle-alpha.bmp"))
      (sdl:copy-channel-to-alpha *particle-img* alpha :channel :r))
```

## Updating Surfaces and the Display ##

The functions [UPDATE-DISPLAY](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#update-display) and [UPDATE-SURFACE](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#update-surface) update the SDL display surface (or OpenGL context) and general SDL surfaces respectively.

# The Cursor #

The the current state of the cursor is returned using [QUERY-CURSOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#query-cursor), and is set using [SHOW-CURSOR](https://lispbuilder.github.io/documentation/lispbuilder-sdl.html#query-cursor).

# A Simple Example #

Putting this all together, we can write short example showing a white rectangle that follows the users mouse movements within an SDL Window. Exit the example by closing the window or pressing the Esc key on the keyboard.

```
(WITH-INIT ()               ; Initialise SDL and the Video subsystem
  (WINDOW 320 240)          ; Open a window, 320 x 240

  (SETF (FRAME-RATE) 30)    ; Lock the frame rate to 30 fps

  (WITH-EVENTS ()
    (:QUIT-EVENT () T)      ; Absolutely have to handle the :QUIT-EVENT,
                            ; or the application will not exit.
    (:KEY-DOWN-EVENT () 
      (WHEN (KEY-DOWN-P :SDL-KEY-ESCAPE) (PUSH-QUIT-EVENT)))

    (:MOUSE-MOTION-EVENT (:X mouse-x :Y mouse-y)
      (CLEAR-DISPLAY *black*)
      ;; Draw the box with center at the mouse x/y coordinates.
      (DRAW-BOX-* (- mouse-x (/ 50 2)) (- mouse-y (/ 50 2)) 50 50 :color *white*))

    (:IDLE ()
      (UPDATE-DISPLAY))))
```

# Object Lifecycles and Garbage Collection #

The [Simple Finalizer](http://github.com/Balooga/Simple-Finalizer) package describes how SDL foreign objects are garbage collected.

# Package Overview #

## Package Exports ##
Functions and symbols exported from the `LISPBUILDER-SDL` package are accessible using the `LISPBUILDER-SDL:` prefix or the shorter form `SDL:` nickname.

## Package Structure ##
The `cffi/` directory contains the raw CFFI bindings. These bindings may be automatically generated by [SWIG](http://www.swig.org) or created by hand depending on the package. CFFI translation functions perform simple low-level conversions for example, converting Lisp strings to C strings and back (see [`translate.lisp`](http://code.google.com/p/lispbuilder/source/browse/trunk/lispbuilder-sdl-image/cffi/translate.lisp) for example).
All functions in `cffi/` accept and return foreign objects only.

The `base/` directory defines wrappers over the functions in `cffi/`.
The functions in `base/` accept keyword arguments and accept `NIL` instead of `CFFI:NULL-POINTER` where appropriate. Generally functions in `base/` accept and return foreign objects. `base/` may perform some type checks `(IS-VALID-PTR SURFACE)` but this layer is meant to be lean. Someone who implements a graphics engine might use this layer instead of `sdl/` if speed is a concern. There are no fancy drawing primitives in this layer.

The `sdl/` directory defines the abstractions over `cffi/` and `base/`. Foreign objects are passed around `sdl/` neatly wrapped in CLOS objects, using [`TRIVIAL-GARBAGE`](http://www.cliki.net/trivial-garbage) for automatic garbage collection (minimize foreign objects being left on the heap). There are no functions in `sdl/` that accept or return foreign objects (with the exception of the functions that create the CLOS wrapper objects). Functions in `sdl/` call down into `cffi/` and `base/` as appropriate. All `LISPBUILDER-SDL` symbols available in `SDL:` are exported from `sdl/`, with symbols imported into `sdl/` from `cffi/` and `base/` as appropriate (e.g. `WITH-EVENTS`). All drawing primitives are defined in this layer; circles, rectangles, lines, triangles, with-bezier etc. Functions in `sdl/` implement a lot of type checking.

An example of the difference between `base/` and `sdl/` is `WITH-RECTANGLE`. The `WITH-RECTANGLE` macro in base/ creates and destroys a foreign `SDL_Rect`. The `WITH-RECTANGLE` macro in `sdl/` will create and destroy a `RECTANGLE` object.


# Examples #

Enter the following at the REPL to compile and load the examples included in the `LISPBUILDER-SDL-EXAMPLES` package:

```
(asdf:operate 'asdf:load-op :lispbuilder-sdl-examples)
```

Run the examples by entering any of the following at the REPL:
```
	  (SDL-EXAMPLES:BEZIER)
	  (SDL-EXAMPLES:BMP-SAMPLE)
	  (SDL-EXAMPLES:CIRCLE-1)
	  (SDL-EXAMPLES:CIRCLE-2)
	  (SDL-EXAMPLES:CIRCLE-3)
	  (SDL-EXAMPLES:CIRCLE-4)
	  (SDL-EXAMPLES:CIRCLE-5)
	  (SDL-EXAMPLES:DISTANCE-2D)
	  (SDL-EXAMPLES:FLOOD-FILL)
	  (SDL-EXAMPLES:INBUILT-FONTS)
	  (SDL-EXAMPLES:LINE-DRAWING)
	  (SDL-EXAMPLES:MANDELBROT)
	  (SDL-EXAMPLES:METABALLS)
	  (SDL-EXAMPLES:MOUSE-2D)
	  (SDL-EXAMPLES:MOUSE-PAINTER)
	  (SDL-EXAMPLES:PIXELS-1)
	  (SDL-EXAMPLES:PIXELS-2)
	  (SDL-EXAMPLES:PIXELS-3)
	  (SDL-EXAMPLES:PIXELS-4)
	  (SDL-EXAMPLES:POINTS-AND-LINES)
	  (SDL-EXAMPLES:RANDOM-RECTS)
	  (SDL-EXAMPLES:RANDOM-BOX-1)
	  (SDL-EXAMPLES:RANDOM-BOX-2)
	  (SDL-EXAMPLES:RANDOM-BOX-3)
	  (SDL-EXAMPLES:RANDOM-BOX-4)
	  (SDL-EXAMPLES:RECURSIVE-RECTS)
	  (SDL-EXAMPLES:SETUP-AND-DRAW)
	  (SDL-EXAMPLES:SIMPLE-FONT-DEMO)
	  (SDL-EXAMPLES:STROKE)
	  (SDL-EXAMPLES:VERTICES)
	  (SDL-EXAMPLES:WIDTH-HEIGHT)
```

# Using OpenGL #

Example of using `LISPBUILDER-SDL` and [CL-OPENGL](http://common-lisp.net/project/cl-opengl/)

```
(asdf:operate 'asdf:load-op :cl-opengl)

(defun opengl-test-1 ()
  (sdl:with-init ()
    (sdl:window 250 250
                :title-caption "OpenGL Example"
                :icon-caption "OpenGL Example"
                :opengl t
                :opengl-attributes '((:SDL-GL-DOUBLEBUFFER 1)))
    (gl:clear-color 0 0 0 0)
    ;; Initialize viewing values.
    (gl:matrix-mode :projection)
    (gl:load-identity)
    (gl:ortho 0 1 0 1 -1 1)
    (sdl:with-events ()
      (:quit-event () t)
      (:idle ()
       (gl:clear :color-buffer-bit)
       ;; Draw white polygon (rectangle) with corners at
       ;; (0.25, 0.25, 0.0) and (0.75, 0.75, 0.0).
       (gl:color 1 1 1)
       (gl:with-primitive :polygon
                          (gl:vertex 0.25 0.25 0)
                          (gl:vertex 0.75 0.25 0)
                          (gl:vertex 0.75 0.75 0)
                          (gl:vertex 0.25 0.75 0))
       ;; Start processing buffered OpenGL routines.
       (gl:flush)
       (sdl:update-display)))))
```

Use the following function to set the OpenGL attributes after SDL has initialized the video subsystem, and before the window is created;

```
(SDL:SET-GL-ATTRIBUTE <attribute> <value>)
```

The following _attributes_ are supported;

  * :SDL-GL-RED-SIZE
  * :SDL-GL-GREEN-SIZE
  * :SDL-GL-BLUE-SIZE
  * :SDL-GL-ALPHA-SIZE
  * :SDL-GL-BUFFER-SIZE
  * :SDL-GL-DOUBLEBUFFER
  * :SDL-GL-DEPTH-SIZE
  * :SDL-GL-STENCIL-SIZE
  * :SDL-GL-ACCUM-RED-SIZE
  * :SDL-GL-ACCUM-GREEN-SIZE
  * :SDL-GL-ACCUM-BLUE-SIZE
  * :SDL-GL-ACCUM-ALPHA-SIZE
  * :SDL-GL-STEREO
  * :SDL-GL-MULTISAMPLEBUFFERS
  * :SDL-GL-MULTISAMPLESAMPLES
  * :SDL-GL-ACCELERATED-VISUAL
  * :SDL-GL-SWAP-CONTROL

The following function provides access to OpenGL. Use **after** an OpenGL context is created.

```
(setf cl-opengl-bindings:*gl-get-proc-address* #'sdl:sdl-gl-get-proc-address)
```

# Creating Standalone Executables #

Tutorials for creating standalone executables using the various Common Lisp development environments;

  * [Creating standalone executables](StandAloneExecutables)
  * [CLISP executable](StandAloneCLISP)
