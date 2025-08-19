title: macOS weight: 12 summary: Instructions for macOS using cmake and
clang tags: \["macos"\] ---

# Building KiCad on macOS

Building on macOS requires care, as specific versions of dependencies
are required in order for all KiCad features to compile and work
properly. To simplify this process, we strongly recommend that all macOS
users use our
[kicad-mac-builder](https://gitlab.com/kicad/packaging/kicad-mac-builder)
utility, which contains the toolchain that produces the official macOS
release builds of KiCad.

This utility can be used to set up a development environment for KiCad
even if you just wish to build the sources without creating a full
release package (DMG containing the KiCad applications and libraries).

Building without `kicad-mac-builder` is possible but is not supported by
the KiCad development team. If you wish to pursue this route, please use
the latest state of the kicad-mac-builder repository for guidance as to
which packages to install and how to configure and build them.

<div class="note">

KiCad requires a custom fork of wxWidgets on macOS to work properly.
This fork is hosted at [the KiCad
GitLab](https://gitlab.com/kicad/code/wxWidgets). Currently, the
`kicad/macos-wx-3.2` branch should be used to build the master branch of
KiCad. If you use `kicad-mac-builder` to set up your build environment,
the correct version of wxWidgets will be downloaded and built for you
automatically.

</div>

## Prerequisites

As of version 7, KiCad requires macOS 11 or higher to build (the build
outputs can be run on some older verisons of macOS). KiCad supports
Apple’s arm64 architecture as well as x86_64, and the packaging scripts
can produce a "universal binary" that will run on either architecture.
You must take care to maintain a consistent architecture when building
on arm64, which `kicad-mac-builder` will try to ensure for you. Building
some of the dependencies for `arm64` and some for `x86_64` will result
in linker errors (or Python errors) later in the process.

## Setting up development environment using kicad-mac-builder

This guide assumes you already have the appropriate Apple C++
development toolchain (Xcode) for your macOS release installed. See
Apple’s documentation for details on how to install Xcode or the Xcode
command-line tools. `kicad-mac-builder` will not install this for you.

To obtain `kicad-mac-builder` and set up the KiCad build dependencies,
run the following on an Apple arm64 machine:

    softwareupdate --install-rosetta --agree-to-license # only required if you did not already install rosetta
    cd <your preferred working directory>
    git clone https://gitlab.com/kicad/packaging/kicad-mac-builder.git
    cd kicad-mac-builder
    ./ci/arm64-on-arm64/bootstrap-arm64-on-arm64.sh
    WX_SKIP_DOXYGEN_VERSION_CHECK=1 ./build.py --arch arm64 --target setup-kicad-dependencies

Or, on an x86_64 (Intel) machine:

    cd <your preferred working directory>
    git clone https://gitlab.com/kicad/packaging/kicad-mac-builder.git
    cd kicad-mac-builder
    ./ci/x86_64-on-x86_64/bootstrap-x86_64-on_x86_64.sh
    WX_SKIP_DOXYGEN_VERSION_CHECK=1 ./build.py --arch x86_64 --target setup-kicad-dependencies

<div class="note">

The first command, `boostrap.sh`, will install Homebrew if it is not
already installed, and install all the KiCad dependencies that are
available as Homebrew packages. You may wish to manually install the
required dependencies instead of running this script. If so, simply
inspect the script to see the current list of required Homebrew packages
to install. The example bootstrap scripts are used for native
compilation on the two Apple architectures. There is also an
`x86_64-on-arm64` option that can be used for cross-compilation on arm64
machines.

</div>

<div class="warning">

The bootstrapping scripts are designed to run in a "clean" installation
of macOS on our CI build machines. If you already have Homebrew
installed to a non-standard directory, do not run `bootstrap.sh`.
Instead, inspect the script and adapt the commands in it to your
environment. If you already have Python installed, especially if you use
a tool like `pyenv` to manage Python versions, you must ensure that the
`python3` that is found first on your `PATH` matches the target
architecture you are building KiCad for.

</div>

At the end of the `kicad-mac-builder` build process, `build.py` will
print a list of suggested CMake arguments to the terminal.

**It is recommended you save either the independent list of variables OR
the toolchain file path. You will need these when configuring your KiCad
build.** The use of the toolchain file means you only have to set
-DCMAKE_TOOLCHAIN_FILE=\<path\> in the cmake arguments later. Future
reruns of KMB will keep the toolchain file updated.

You can rerun build.py to get all the variables again at any time.

## Building KiCad

Now you are ready to build KiCad itself. Run the following to obtain and
build KiCad:

    cd <your preferred working directory>
    git clone https://gitlab.com/kicad/code/kicad.git
    cd kicad
    mkdir -p build/release
    mkdir build/debug               # Optional for debug build.
    cd build/release
    cmake <arguments from kicad-mac-builder> ../..
    make
    make install

Paste in the arguments you saved from earlier after the `cmake` command.
Note that you may want to inspect and modify some of the arguments
before using them:

By default, `kicad-mac-builder` will set the `CMAKE_INSTALL_PREFIX` to
`./build/kicad-dest` inside your `kicad-mac-builder` directory. Edit the
arguments provided by `kicad-mac-builder` if you want to install KiCad
to a different location.

By default, `kicad-mac-builder` will set `CMAKE_BUILD_TYPE` to
`RelWithDebInfo` to create an optimized release build with debugging
symbols. Change this to `Debug` to create an unoptimized build that can
be debugged using `lldb`.

<div class="note">

We recommend using Ninja instead of `make` for faster builds. To do so,
run `brew install ninja` and then add `-G Ninja` to your `cmake`
arguments.

</div>

## Running and debugging

On macOS, starting from 7.99 nightlies, you can set the
`KICAD_RUN_FROM_BUILD_DIR` environment variable and launch any KiCad
executable from the build directory for run or debug. This is generally
done in the target launch configuration options of IDEs like CLion.

You can also create and run the entire app bundle by running
`make install` (or `ninja install`) to package a complete `KiCad.app`
and then launch that. You can use `lldb` to debug the application, or
attach to a running copy.

The installed package will be self-signed by the installation process.
This will allow it to run on the local machine, but this package cannot
be distributed to other machines without triggering Apple’s security
measures.

## Updating dependencies

At the moment, `kicad-mac-builder` is designed to run in a "clean-slate"
environment and does not automatically handle dependency updates. From
time to time, you may wish to update your KiCad build dependencies. To
do so:

1.  Run `git pull` inside your `kicad-mac-builder` folder to sync with
    KiCad upstream.

2.  Delete the contents of the `./build` directory.

3.  Re-run the `setup-kicad-dependencies` step as described above.

<div class="note">

It is possible to rebuild only certain dependencies by deleting certain
folders inside the `./build` directory, however this is an advanced
usage and will not be documented here. Inspect the `cmake` files inside
the `kicad-mac-builder` subdirectory to figure out what to do.

</div>
