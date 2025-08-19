title: Getting Started weight: 10 ---

# Development Tools

Before you begin building KiCad, there are a few tools required in
addition to your compiler. Some of these tools are required to build
from source and some are optional.

## CMake Build Configuration Tool

[CMake](https://cmake.org) is the build configuration and makefile
generation tool used by KiCad. It is required.

## Git Version Control System

The official source code repository is hosted on
[GitLab](https://gitlab.com/) and requires [git](https://git-scm.com/)
to get the latest source. If you prefer to use
[GitHub](https://github.com/) there is a read only mirror of the
official KiCad repository. The previous official hosting location at
[Launchpad](https://launchpad.net/kicad/) is still active as a mirror.
Changes should be submitted as [merge
requests](https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html)
via GitLab. The development team will not review changes submitted on
GitHub or Launchpad as those platforms are mirrors only.

## Doxygen Code Documentation Generator

The KiCad source code is documented using
[Doxygen](https://www.doxygen.nl/index.html) which parses the KiCad
source code files and builds a dependency tree along with the source
documentation into HTML. Doxygen is only required if you are going to
build the KiCad documentation.

## SWIG Simplified Wrapper and Interface Generator

[SWIG](http://www.swig.org/) is used to generate the Python scripting
language extensions for KiCad. SWIG is not required if you are not going
to build the KiCad scripting extension.

# Library Dependencies

This section includes a list of library dependencies required to build
KiCad. It does not include any dependencies of the libraries. Please
consult the library’s documentation for any additional dependencies.
Some of these libraries are optional depending on you build
configuration. This is not a guide on how to install the library
dependencies using you systems package management tools or how to build
the library from source. Consult the appropriate documentation to
perform these tasks.

## wxWidgets Cross Platform GUI Library

[wxWidgets](http://wxwidgets.org/) is the graphical user interface (GUI)
library used by KiCad. As of version 7.0, KiCad is developed and tested
against wxWidgets 3.2. The KiCad team will not perform testing or
bugfixing against older versions of wxWidgets, and may inadvertently
introduce changes that break compilation or function when building
against older versions. We welcome patches to fix build issues against
earlier versions of wxWidgets as long as these patches do not change
functionality when compiling against more modern versions. These patches
should be the minimum necessary to get KiCad working, rather than
attempting to backport functionality from the newer wxWidgets API
(unless doing so is trivial).

On macOS we use a custom fork of wxWidgets — see the macOS build
instructions for details.

## Boost C++ Libraries

The [Boost](https://www.boost.org/) C++ library version 1.71 or greater
is required to build KiCad.

## GLEW OpenGL Extension Wrangler Library

The [OpenGL Extension Wrangler](http://glew.sourceforge.net/) is an
OpenGL helper library used by the KiCad graphics abstraction library
(GAL) and is always required to build KiCad.

## ZLib Library

The [ZLib](http://www.zlib.net/) development library is used by KiCad to
handle compressed 3d models (.stpz and .wrz files) and is always
required to build KiCad.

## GLM OpenGL Mathematics Library

The [OpenGL Mathematics Library](http://glm.g-truc.net/) is an OpenGL
helper library used by the KiCad graphics abstraction library (GAL) and
is always required to build KiCad.

## GLUT OpenGL Utility Toolkit Library

The [OpenGL Utility
Toolkit](https://www.opengl.org/resources/libraries/glut/) is an OpenGL
helper library used by the KiCad graphics abstraction library (GAL)and
is always required to build KiCad.

## Cairo 2D Graphics Library

The [Cairo](http://cairographics.org/) 2D graphics library is used as a
fallback rendering canvas when OpenGL is not available and is always
required to build KiCad.

## Python Programming Language

The [Python](https://www.python.org/) programming language is used to
provide scripting support to KiCad. It is always required to build
KiCad.

## wxPython Library

The [wxPython](http://wxpython.org/) library is used to provide a
scripting console for Pcbnew. It needs to be installed unless the
[wxPython scripting](#wxpython_scripting) build configuration option is
disabled. When building KiCad with wxPython support, make sure the
version of the wxWidgets library and the version of wxPython installed
on your system are the same. Mismatched versions have been known to
cause runtime issues.

## Curl Multi-Protocol File Transfer Library

The [Curl Multi-Protocol File Transfer
Library](http://curl.haxx.se/libcurl/) is used to provide secure
internet file transfer access for the Plugin and Content Manager.

## OpenCascade Library

[Open CASCSADE Technology
(OCC)](https://www.opencascade.com/content/overview) is used to provide
support for loading and saving 3D model file formats such as STEP. KiCad
requires OCC 7.5.0 or higher. When building OCC locally, use the option
BUILD_MODULE_Draw=OFF to make building easier

### Ngspice Library

The [Ngspice Library](https://sourceforge.net/projects/ngspice/) is used
to provide Spice simulation support in the schematic editor. Make sure
the the version of ngspice library used was built with the—​with-ngshared
option. This library needs to be installed unless the Spice build option
is disabled.

### Protocol Buffers (protobuf)

The [protobuf](https://protobuf.dev/) library is used to provide
structured serialization of KiCad data for the IPC API. It is required
even when the IPC API is not enabled, as it is also used as part of QA
testing and is likely to be used in other applications in the future.
KiCad requires protobuf version 3.x, and must be built with the same
version of protobuf that is deployed as a library on the target system.

### Nanomsg Next Generation (nng)

The [nng](https://nng.nanomsg.org/) library is used to provide a
communications channel between KiCad and third-party plugins and
applications for the IPC API. It is required when KICAD_IPC_API is
enabled.

# KiCad Build Configuration Options

KiCad has many build options that can be configured to build different
options depending on the availability of support for each option on a
given platform. This section documents these options and their default
values.

## IPC API

The KICAD_IPC_API option is used to enable the experimental IPC API
server. When this option is enabled, the [NNG](#nng) library must be
available.

## wxPython Scripting Support

The KICAD_SCRIPTING_WXPYTHON option is used to enable building the
wxPython interface into Pcbnew including the wxPython console.

## Integrated Spice simulator

The KICAD_SPICE option is used to control if the Spice simulator
interface for Eeschema is built. When this option is enabled, it
requires [Ngspice](#ngspice) to be available as a shared library. This
option is enabled by default.

## STEP/IGES support for the 3D viewer

The KICAD_USE_OCC is used for the 3D viewer plugin to support STEP and
IGES 3D models. Build tools and plugins related to OpenCascade (OCC) are
enabled with this option. When enabled it requires
[OpenCascade](#libocct) to be available. This option is enabled by
default.

## Wayland EGL support

The KICAD_USE_EGL option switches the OpenGL backend from using X11
bindings to Wayland EGL bindings. This option is only relevant on Linux
when running wxWidgets 3.1.5+ with the EGL backend of the wxGLCanvas
(which is the default option, but can be disabled in the wxWidgets
build).

By default, setting KICAD_USE_EGL will use a in-tree version of the GLEW
library (that is compiled with the additional flags needed to run on an
EGL canvas) staticly linked into KiCad. If the system version of GLEW
supports EGL (it must be compiled with the GLEW_EGL flag), then it can
be used instead by setting KICAD_USE_BUNDLED_GLEW to OFF.

## Windows HiDPI Support

The KICAD_WIN32_DPI_AWARE option makes the Windows manifest file for
KiCad use a DPI aware version, which tells Windows that KiCad wants Per
Monitor V2 DPI awareness (requires Windows 10 version 1607 and later).

## Development Analysis Tools

KiCad can be compiled with support for several features to aid in the
catching and debugging of runtime memory issues

### Valgrind support

The KICAD_USE_VALGRIND option is used to enable Valgrind’s stack
annotation feature in the tool framework. This provides the ability for
Valgrind to trace memory allocations and accesses in the tool framework
and reduce the number of false positives reported. This option is
disabled by default.

### C++ standard library debugging

KiCad provides two options to enable debugging assertions contained in
the GCC C++ standard library: KICAD_STDLIB_DEBUG and
KICAD_STDLIB_LIGHT_DEBUG. Both these options are disabled by default,
and only one should be turned on at a time with KICAD_STDLIB_DEBUG
taking precedence.

The KICAD_STDLIB_LIGHT_DEBUG option enables the light-weight standard
library assertions by passing `_GLIBCXX_ASSERTIONS` into CXXFLAGS. This
enables things such as bounds checking on strings, arrays and vectors,
as well as null pointer checks for smart pointers.

The KICAD_STDLIB_DEBUG option enables the full set of standard library
assertions by passing `_GLIBCXX_DEBUG` into CXXFLAGS. This enables full
debugging support for the standard library.

### Address Sanitizer support

The KICAD_SANITIZE_ADDRESS option enables [Address Sanitizer
(ASan)](https://clang.llvm.org/docs/AddressSanitizer.html) support to
trace memory allocations and accesses to identify problems. This option
is disabled by default. The Address Sanitizer contains several runtime
options to tailor its behavior that are described in more detail in its
[documentation](https://github.com/google/sanitizers/wiki/AddressSanitizerFlags).

Analogously, the KICAD_SANITIZE_THREADS option enables [Thread Sanitizer
(TSan)](https://clang.llvm.org/docs/ThreadSanitizer.html). Its runtime
options are described
[here](https://github.com/google/sanitizers/wiki/ThreadSanitizerFlags).

These options are not supported on all build systems, and are known to
have problems when using MinGW. They may also cause errors when using a
linker other than the GNU linker, for example Gold, Lld, Mold.

## Demos and Examples

The KiCad source code includes some demos and examples to showcase the
program. You can choose whether install them or not with the
KICAD_INSTALL_DEMOS option. You can also select where to install them
with the KICAD_DEMOS variable. On Linux the demos are installed in
\$PREFIX/share/kicad/demos by default.

## Quality assurance (QA) unit tests

The KICAD_BUILD_QA_TESTS option allows building unit tests binaries for
quality assurance as part of the default build. This option is enabled
by default.

If this option is disabled, the QA binaries can still be built by
manually specifying the target. For example, with `make`:

- Build all QA binaries: `make qa_all`

- Build a specific test: `make qa_pcbnew`

- Build all unit tests: `make qa_all_tests`

- Build all test tool binaries: `make qa_all_tools`

For more information about testing KiCad, see \[this page\](testing.md).

## KiCad Build Version

The KiCad version string is defined by the output of
`git describe --dirty` when git is available or the version string
defined in CMakeModules/KiCadVersion.cmake with the value of
KICAD_VERSION_EXTRA appended to the former. If the KICAD_VERSION_EXTRA
variable is not defined, it is not appended to the version string. If
the KICAD_VERSION_EXTRA variable is defined it is appended along with a
leading '-' to the full version string as follows:

    (KICAD_VERSION[-KICAD_VERSION_EXTRA])

The build script automatically creates the version string information
from the [Git](#git) repository information as follows:

    (5.0.0-rc2-dev-100-g5a33f0960)
     |
     output of "git describe --dirty" if git is available.

## KiCad Config Directory

The default KiCad configuration directory is `kicad`. On Linux this is
located at `~/.config/kicad`, on MSW, this is
`C:\Documents and Settings\username\Application Data\kicad` and on
MacOS, this is `~/Library/Preferences/kicad`. Inside the configuration
directory, subdirectories will be created for each KiCad minor version,
meaning that multiple versions of KiCad can share the same directory.

The base configuration directory can be overridden by specifying the
KICAD_CONFIG_DIR string at compile time.

<div class="note">

Setting KICAD_CONFIG_DIR should be considered deprecated as of KiCad
5.99, as the config directory is versioned and there should not be any
need to set a custom directory.

</div>

## Running from the Build Directory

Normally, KiCad needs to be installed before running in order to locate
data files and shared libraries. Developers may be interested in running
specific KiCad binaries from inside the build directory instead of
installing, as this can sometimes be a faster way to test things. The
environment variable `KICAD_RUN_FROM_BUILD_DIR` can be set in order to
change how KiCad looks up paths for shared libraries, resources, and
other data files. Note that setting this variable does not change how
KiCad looks for symbol/footprint/3D model libraries.

## Setting the path to Python

KiCad relies on a specific Python version on Windows and macOS.
Normally, the path to this Python installation is set by the
corresponding packaging scripts for those platforms, but in some
situations, it can be preferable to set a custom Python interpreter for
development or testing purposes. On Windows, you must set the
environment variable `KICAD_USE_EXTERNAL_PYTHONHOME` in order for KiCad
to use the `PYTHONHOME` environment variable instead of the default
(hard-coded) path to Python. This is so that `PYTHONHOME` set on user
machines does not inadvertently break KiCad. See the Windows build
instructions for details on how to use this variable to run KiCad from
the build directory.

# Getting the KiCad Source Code

There are several ways to get the KiCad source. If you want to build the
stable version you can down load the source archive from the
[GitLab](https://gitlab.com/kicad/code/kicad/) repository. Use tar or
some other archive program to extract the source on your system. If you
are using tar, use the following command:

``` sh
tar -xaf kicad_src_archive.tar.xz
```

If you are contributing directly to the KiCad project on GitLab, you can
create a local copy on your machine by using the following command:

``` sh
git clone https://gitlab.com/kicad/code/kicad.git
```

Here is a list of source links:

Stable release archives: <https://kicad.org/download/source/>

Development branch: <https://gitlab.com/kicad/code/kicad/tree/master>

GitHub mirror: <https://github.com/KiCad/kicad-source-mirror>
