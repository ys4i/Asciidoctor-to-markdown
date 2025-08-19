title: Windows (MSYS2) weight: 13 summary: Guide on building KiCad using
MSYS2 tags: \["windows"\] ---

# Building using MSYS2

<div class="warning">

MSYS2 is the legacy build system for Windows due to KiCad’s historical
roots. KiCad has switched to MSVC (Microsoft Visual Studio) from version
6.0 and onwards for development and releases. Building via MSYS2 is
still possible but does not receive a high level of attention.

</div>

## Setup

The [MSYS2](https://www.msys2.org/) project provides packages for all of
the require dependencies to build KiCad. To setup the MSYS2 build
environment, download and run the **MSYS2 64-bit Installer** available
from the msys2 home page. After the installer is finished, update to the
latest package versions by running the `msys2_shell.cmd` file located in
the MSYS2 install path and running the command `pacman -Syu`. If the
msys2-runtime package is updated, close the shell and run
`msys2_shell.cmd`.

## Building

The following commands assume you are building for 64-bit Windows, and
that you already have the KiCad source code in a folder called
`kicad-source` in your home directory. See below for changes if you need
to build for 32-bit instead. At the command prompt run the the following
commands:

``` bash
pacman -S base-devel \
          git \
          mingw-w64-ucrt-x86_64-cmake \
          mingw-w64-ucrt-x86_64-doxygen \
          mingw-w64-ucrt-x86_64-gcc \
          mingw-w64-ucrt-x86_64-python \
          mingw-w64-ucrt-x86_64-pkg-config \
          mingw-w64-ucrt-x86_64-swig \
          mingw-w64-ucrt-x86_64-boost \
          mingw-w64-ucrt-x86_64-cairo \
          mingw-w64-ucrt-x86_64-glew \
          mingw-w64-ucrt-x86_64-curl \
          mingw-w64-ucrt-x86_64-wxPython \
          mingw-w64-ucrt-x86_64-toolchain \
          mingw-w64-ucrt-x86_64-glm \
          mingw-w64-ucrt-x86_64-opencascade \
          mingw-w64-ucrt-x86_64-ngspice \
          mingw-w64-ucrt-x86_64-zlib \
          mingw-w64-ucrt-x86_64-libgit2 \
          mingw-w64-ucrt-x86_64-wxwidgets3.2-msw \
          mingw-w64-ucrt-x86_64-protobuf \
          mingw-w64-ucrt-x86_64-nng
cd kicad-source
mkdir -p build/release
mkdir build/debug               # Optional for debug build.
cd build/release
cmake -DCMAKE_BUILD_TYPE=Release \
      -G "MSYS Makefiles" \
      -DCMAKE_PREFIX_PATH=/ucrt64 \
      -DCMAKE_INSTALL_PREFIX=/ucrt64 \
      -DDEFAULT_INSTALL_PATH=/ucrt64 \
      -DOCC_INCLUDE_DIR=/ucrt64/include/opencascade \
      ../../
make -j N install   # Where N is the number of concurrent threads that your system can handle.
```

<div class="note">

Since `nng` has only provided packages for `CLANG64`, `CLANGARM64`, and
`UCRT64` environments, this article recommends `UCRT64` as the building
environment.

</div>

For 32-bit builds, run `mingw32.exe` and change `ucrt-x86_64` to `i686`
in the package names and change the paths in the cmake configuration
from `/ucrt64` to `/mingw32`.

<div class="note">

msys2 has deprecated 32-bit support due to cygwin, it’s upstream
dependency eliminating 32-bit support.

</div>

For debug builds, run the cmake command with `-DCMAKE_BUILD_TYPE=Debug`
from the `build/debug` folder.

## MSYS2 with CLion

KiCad in combiation with MSYS2 can be configured to be used with CLion
to provide a nice IDE experience.

### Toolchain Setup

First you must register MSYS2 as a toolchain, or namely, the compiler.

File \> Preferences to open the Settings window.

Navigate to Build, Execution, Development and then the Toolchains page.

Add a new toolchain, and configure it as such

- Name: `MSYS2-MinGW64`

- Environment Path: `<your msys2 install folder>\mingw64\`

- CMake: `<your msys2 install folder>\mingw64\bin\cmake.exe`

All other fields will become automatically populated.

### Project Setup

File \> Open and select the folder containing the kicad source. CLion
may attempt to start CMake generation and fail, this is ok.

Open the Settings window again. Navigate to Build, Execution,
Development and then the CMake page. These settings are saved to the
project.

You want to create a Debug configuration as such

- Name: `Debug-MSYS2`

- Build-Type: `Debug`

- Toolchain: `MSYS2-MinGW64`

- CMake options:

``` sh
-G "MinGW Makefiles"
-DCMAKE_PREFIX_PATH=/mingw64
-DCMAKE_INSTALL_PREFIX=/mingw64
-DDEFAULT_INSTALL_PATH=/mingw64
```

- Build-directory: `build/debug-msys2`

You may now trigger the "Reload CMake Cache" option in CLion to generate
the cmake project You should delete the "junk" build folder (usually
name cmake-build-debug-xxxx) it may have created in the source before it
was changed above. We change the build folder because we have a
gitignore for `/build`

Warning: Receiving warning messages about Boost versions is normal.

## Known MSYS2 Build Issues

There are some known issues that are specific to MSYS2. This section
provides a list of the currently known issues when building KiCad using
MSYS2.

### wxwidgets 3.1

You get an error that wxwidgets 3.1 is not found

    pacman -R mingw-w64-x86_64-wxwidgets
    rm -rf build/release    # change to your cmake cache folder
    pacman -S mingw-w64-x86_64-wxmsw3.1
