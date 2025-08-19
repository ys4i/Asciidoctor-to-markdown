title: Linux weight: 11 summary: Instructions for Linux using gcc tags:
\["linux"\] ---

# Building KiCad on Linux

To perform a full build on Linux, run the following commands:

    cd <your kicad source mirror>
    mkdir -p build/release
    mkdir -p build/debug               # Optional for debug build.
    cd build/release
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo \
            ../../
    make
    sudo make install

If the CMake configuration fails, determine the missing dependencies
(see [Dependencies](#_dependencies)) and install them on your system.

<div class="note">

If CMake fails at finding Protobuf even after installing the
dependencies, try enabling `KICAD_USE_CMAKE_FINDPROTOBUF`, i.e.:

    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo \
          -DKICAD_USE_CMAKE_FINDPROTOBUF=ON \
            ../../

</div>

By default, CMake sets the install path on Linux to `/usr/local`. Use
the `CMAKE_INSTALL_PREFIX` option to specify a different install path.

We recommend using the `RelWithDebInfo` build type for personal release
builds, as this will include debugging symbols that will give you more
useful stack traces in case you encounter a crash.

Replace `RelWithDebInfo` with `Debug` for debug builds.

## Dependencies

The distribution-specific packages that have to be installed to build
KiCad can be found in the nightly package sources:

- [Debian/Ubuntu/Linux
  Mint](https://gitlab.com/kicad/packaging/kicad-ubuntu-builder/kicad-daily-package/-/blob/dailybuild/debian/control)
  (`Depends` and `Build-Depends` fields)

- [Fedora
  Linux](https://gitlab.com/kicad/packaging/kicad-fedora-builder/-/blob/master/templates/kicad-nightly.spec)
  (`Requires` and `BuildRequires` fields)

- [Arch
  Linux/Manjaro](https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=kicad-nightly)
  (`depends` and `makedepends` fields)

## Tips and Tricks

### Ninja

KiCad builds faster using the Ninja build system in place of `make`. To
use Ninja, you can specify Ninja output on your CMake command line:

    cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo ../../
    ninja
    sudo ninja install

### Linker

Build speed will be further improved by using a linker different than
the default BFD linker (`ld`), for example `gold` (fast), `lld`
(faster), or `mold` (fastest). To change the linker, specify it by
passing the `-fuse-ld=<linker name>` flag via the `-DCMAKE_CXX_FLAGS`
CMake option:

    cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_CXX_FLAGS=-fuse-ld=lld ../../

GCC versions lower than 12.1.0 do not support `-fuse-ld=mold`. Read
<https://github.com/rui314/mold#how-to-use> to learn how to use `mold`
in that case.
