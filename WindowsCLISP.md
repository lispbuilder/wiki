

# Overview #

This page describes the installation and configuration of CLISP and LISPBUILDER-SDL for Windows.

# Install  CLISP #

Download [CLISP](http://clisp.cons.org/) for Windows;

  * [CLISP sources and binaries for Windows](http://sourceforge.net/project/platformdownload.php?group_id=1355)

Install CLISP to the default directory; from now on this directory will be denoted as:

```
[CLISP]
```

Note: Here, `[CLISP]` means to insert the installation directory (e.g. "C:\Program Files\clisp-X.Y\"), not the literal text "`[CLISP]`".

Wherever you put CLISP, it is important to set the environment variable PATH. Fortunately, the installer automatically does this, so just make sure to check the option in the installer.

Also note that in order for these changes to take effect, you may need to restart your computer.

# Install ASDF #

[ASDF](http://common-lisp.net/project/asdf/) is a system which assists with compiling and loading libraries. Since it does not come with CLISP by default, download it from

  * http://cclan.cvs.sourceforge.net/*checkout*/cclan/asdf/asdf.lisp

And place the file in

```
[CLISP]\site\asdf.lisp
```

# Install CFFI and Dependencies #

Download the latest version of [CFFI](http://common-lisp.net/project/cffi/);

  * [Official release tarballs](http://common-lisp.net/project/cffi/releases/?M=D)

The latest version of CFFI has three dependencies that must also be downloaded;

  * [ALEXANDRIA](http://www.cliki.net/Alexandria)
  * [BABEL](http://www.cliki.net/Babel)
  * [TRIVIAL-FEATURES](http://www.cliki.net/trivial-features)

Extract these packages to the following directories;

```
[CLISP]\site\cffi\
[CLISP]\site\alexandria\
[CLISP]\site\babel\
[CLISP]\site\trivial-features\
```

If you do not already have a program which can extract tar.gz files (necessary for extracting the above packages), [7-Zip](http://www.7-zip.org/) is a good option.

# Install LISPBUILDER-SDL and Binaries #

Download `LISPBUILDER-SDL` from [source](http://code.google.com/p/lispbuilder/source/checkout) (requires SVN), or [downloads](http://code.google.com/p/lispbuilder/downloads/list) above. Install to;

```
[CLISP]\site\lispbuilder-sdl\
```

Download the required SDL binaries;

  * _win32-lispbuilder-sdl-binaries.tgz_ from [downloads](http://code.google.com/p/lispbuilder/downloads/list) above, or
  * SDL from the [official site](http://www.libsdl.org) or another package

Install to the following directory;

```
[CLISP]\site\lispbuilder-sdl\
```

# Create an CLISP User Initialization File #

Create a new `.clisprc.lisp` file in your home directory;

```
[HOME]\.clisprc.lisp
```

If you are unaware which directory CLISP considers your home directory, start CLISP and execute;

```
> clisp
...
Welcome to GNU CLISP X.Y (YYYY-MM-DD) <http://clisp.cons.org/>
...
[1]> (user-homedir-pathname)
#P"C:\\Documents and Settings\\Your Name\\"
```

Add the following to the `.clisprc.lisp` file above;

```
(load (compile-file "[CLISP]\\site\\asdf.lisp"))

;; put all subdirectories of [CLISP]\site\ into asdf:*central-registry*
(dolist (dir (directory "[CLISP]\\site\\*\\"))
  (pushnew dir asdf:*central-registry* :test #'equal))
 
;; load lispbuilder-sdl
(asdf:operate 'asdf:load-op :lispbuilder-sdl)
(asdf:operate 'asdf:load-op :lispbuilder-sdl-binaries)
```

Note that you'll have to double up the backslashes in `[CLISP]` to avoid confusing CLISP.

# Try the Examples #

To make sure your configuration works, start CLISP and try one of the examples;

```
> clisp
...
Welcome to GNU CLISP X.Y (YYYY-MM-DD) <http://clisp.cons.org/>
...

* (asdf:operate 'asdf:load-op :lispbuilder-sdl-examples)
...

* (sdl-examples:mandelbrot)
```

# Emacs and SLIME #

If you don't already have an editor you use for Lisp code, [Emacs](http://www.gnu.org/software/emacs/) and [SLIME](http://common-lisp.net/project/slime/) are a good combination.
