# Introduction #

This how-to guide was originally written by Elliott Slaughter and posted on the mailing list. It has been reproduced here so that others can make use of it. The guide describes how to connect the Emacs/SLIME IDE to Lisp and simultaneously run a game while using the interactive console, debugger, and editor features of SLIME.

# How To: Portable Interactive Development with SLIME #

Interactive development is one of the frequently touted reasons to do game development in Lisp. That said, I've found it difficult to interactive development in a portable way across different Lisp implementations, especially ones which lack multi-threading. I now believe I have found a fairly robust and portable solution, which I would like to share. First, however, a little bit on why I don't like other solutions I've seen:

One solution I've seen many use is to simply use the implementation's REPL and spawn either the REPL or game loop in a thread. The problem with this is that getting this to work is implementation specific, and can't possibly work without threads. (SBCL on Windows is one of my major development platforms, and doesn't support threads.) Also, I tend not to like the implementation-specific IDEs that come with various Lisp, which tend not to be as full-featured as Emacs with SLIME.

Another solution which I used in my last game was to write a REPL into the main game loop of the game. While this worked, it added a lot of unnecessary complexity to the game engine. Also, a REPL is not a replacement for an IDE, and doesn't make it easy to edit source files and see those changes instantaneously. Adding readline-like support was extra work, and the minimal debugger I implemented wasn't really useful for more that selecting restarts.

What I realized is that I really wanted just to have a SLIME connection to the game, and use that as my REPL/IDE. Unfortunately, although SLIME has a :spawn option for multi-threaded Lisps, this won't work on single-threaded Lisps, where requests will just get pipelined until after the main game loop has terminated. After discussing this with the SLIME developers, I came up with the following:

```
(let ((connection
       (or swank::*emacs-connection* (swank::default-connection))))
  (when (and connection (not (eql swank:*communication-style* :spawn)))
    (swank::handle-requests connection t)))
```

Calling the above in your main game loop will allow SLIME to process requests, even on single-threaded Lisps, which would otherwise be blocked until main returns.

Also, it would probably be desirable to use conditional compilation to avoid having this compiled into your application when delivering a final product. I use the following:

```
(eval-when (:compile-toplevel :load-toplevel :execute)
  (when (find-package :swank)
    (pushnew :my-game-debug *features*)))

#+my-game-debug
... ; handle requests
```

This way, you can just start the game in SLIME and use REPL, debugger, and even modify the source code while the game is running and see changes immediately!

Finally, here are a couple of practical suggestions for making the most of interactive development.

  * Use defvar rather than defparameter for global data. That way the current game data won't accidentally get overridden if you recompile a file.
  * Use defmethod rather than defun for functions you might want to recompile at runtime. That way, if you keep a reference to a generic function, associated methods will be updated automatically when you recompile. Recompiling a normal function will create a new function object, so references kept to normal functions won't update automatically in this manner.
  * Learn SLIME keybindings! C-c C-c (compile function) and C-c C-k (compile file) are very useful!
