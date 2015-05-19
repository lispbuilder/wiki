

# Overview #

This page describes the installation and configuration of SBCL and LISPBUILDER-SDL for Windows.

# Install  SBCL #

Download [SBCL](http://www.sbcl.org/) for Windows;

  * [SBCL binaries for various OSes (including Windows)](http://www.sbcl.org/platform-table.html)

Install SBCL to the default directory; from now on this directory will be denoted as:

```
[SBCL]
```

Note: Here, `[SBCL]` means to insert the installation directory (e.g. "C:\Program Files\Steel Bank Common Lisp\X.Y.Z\"), not the literal text "`[SBCL]`".

# Install CFFI and Dependencies #

Download the latest version of [CFFI](http://common-lisp.net/project/cffi/);

  * [Official release tarballs](http://common-lisp.net/project/cffi/releases/?M=D)

The latest version of CFFI has three dependencies that must also be downloaded;

  * [ALEXANDRIA](http://www.cliki.net/Alexandria)
  * [BABEL](http://www.cliki.net/Babel)
  * [TRIVIAL-FEATURES](http://www.cliki.net/trivial-features)

Extract these packages to the following directories;

```
[SBCL]\site\cffi\
[SBCL]\site\alexandria\
[SBCL]\site\babel\
[SBCL]\site\trivial-features\
```

If you do not already have a program which can extract tar.gz files (necessary for extracting the above packages), [7-Zip](http://www.7-zip.org/) is a good option.

# Install LISPBUILDER-SDL and Binaries #

Download `LISPBUILDER-SDL` from [source](http://code.google.com/p/lispbuilder/source/checkout) (requires SVN), or [downloads](http://code.google.com/p/lispbuilder/downloads/list) above. Install to;

```
[SBCL]\site\lispbuilder-sdl\
```

Download the required SDL binaries;

  * _win32-lispbuilder-sdl-binaries.tgz_ from [downloads](http://code.google.com/p/lispbuilder/downloads/list) above, or
  * SDL from the [official site](http://www.libsdl.org) or another package

Install to the following directory;

```
[SBCL]\site\lispbuilder-sdl\
```

# Create an SBCL System Initialization File #

Create a new `sbclrc` file;

```
[SBCL]\sbclrc
```

Add the following to the `sbclrc` file above;

```
(require :asdf)

;; put all subdirectories of [SBCL]\site\ into asdf:*central-registry*
(dolist (dir (directory "[SBCL]\\site\\*\\"))
  (pushnew dir asdf:*central-registry* :test #'equal))
 
;; load lispbuilder-sdl
(asdf:operate 'asdf:load-op :lispbuilder-sdl)
(asdf:operate 'asdf:load-op :lispbuilder-sdl-binaries)
```

Note that you'll have to double up the backslashes in `[SBCL_HOME]` to avoid confusing SBCL.

# Try the Examples #

To make sure your configuration works, start SBCL and try one of the examples;

```
> sbcl
This is SBCL X.Y.Z, an implementation of ANSI Common Lisp.
...

* (asdf:operate 'asdf:load-op :lispbuilder-sdl-examples)
...

* (sdl-examples:mandelbrot)
```

# Emacs and SLIME #

If you don't already have an editor you use for Lisp code, [Emacs](http://www.gnu.org/software/emacs/) and [SLIME](http://common-lisp.net/project/slime/) are a good combination.

Note: There is a bug in the current version of SBCL (1.0.22 as of the time of writing) which causes SLIME to misbehave. A description of the problem, and a workaround, can be found in the [slime-devel archives](http://article.gmane.org/gmane.lisp.slime.devel/7991).
