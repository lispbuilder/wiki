# Compatibility #

| **Lisp Implementation** | **Win32** | **Linux** | **OS X** | **BSD** | **Comments** |
|:------------------------|:----------|:----------|:---------|:--------|:-------------|
| SBCL                    | Yes       | Yes       | Yes      | Yes     | Win32 version is single threaded.|
| Lispworks               | Yes       | Yes       | Yes      | ??      |              |
| Allegro                 | Yes       | Yes       | Yes      | ??      |              |
| CMUCL                   | NA        | Yes       | ??       | ??      |              |
| CLISP                   | Yes       | Yes       | Yes      | Yes     |              |
| Clozure CL              | Yes       | Yes       | Yes`*`   | ??      | `*`CCL on OS X requires [a single-threaded build](http://common-lisp.net/project/qitab/) in order to avoid thread safety issues with Cocoa. |
| ECL                     | Yes       | Yes       | No       | ??      | ECL (CVS as of 2009-08-14) compiles on MinGW. ECL on OS X breaks in cocoahelper. |

# Download & Installation for Windows #

For newbies, [Lispworks with the Lisp Starter Pack](WindowsLispworks) is suggested.

See [Windows Installation](WindowsInstallation) for guides to installing other Lisp implementations.

# Download & Installation for OS X, Linux and BSD #

The `LISPBUILDER-SDL` packages rely on several external dynamic linked libraries that are not included as part of the `LISPBUILDER-SDL` distribution. These libraries must be installed prior to loading any of the `LISPBUILDER-SDL` packages. Additional information on the dependencies for a package as well as the download & installation procedures are are [described here](DownloadInstallation).
