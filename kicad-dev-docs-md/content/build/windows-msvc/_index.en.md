title: Windows (Visual Studio) weight: 14 summary: Guide on building
KiCad using Microsoft Visual Studio and vcpkg tags: \["windows"\] ---
:toc:

# Building using Visual Studio (2019, 2022)

## Environment Setup

### Visual Studio

You must first install [Visual
Studio](https://visualstudio.microsoft.com/vs/) with the **Desktop
development with C++** feature set installed. Additionally, you’ll need
to make sure the optional component [C++ CMake tools for
Windows](https://docs.microsoft.com/en-us/cpp/build/cmake-projects-in-visual-studio?view=msvc-160#installation)
is installed.

### vcpkg

KiCad uses vcpkg for dependencies and uses **manifest mode** to declare
the dependencies and our kicad registry on top of vcpkg.

Obtain vcpkg for your system from <https://github.com/microsoft/vcpkg>
or

    git clone https://github.com/microsoft/vcpkg.git
    .\vcpkg\bootstrap-vcpkg.bat

<div class="note">

KiCad requires a vcpkg.exe tool released since Jan 2022, therefore
ensure any existing copy of vcpkg is up to date and bootstrap it again
if required.

</div>

## KiCad Specific Setup

### 1. CMakeSettings.json

Contained in the build root is a `CMakeSettings.json.sample`, copy and
rename this file to `CMakeSettings.json` Edit `CMakeSettings.json`
update the VcPkgDir environment variable up top to match the location of
your vcpkg clone.

``` json
{ "VcPkgDir": "D:/vcpkg/" },
```

### 2. "Open Folder" in Visual Studio

- Launch Visual Studio (only after completing the above steps).

- When the initial wizard launches, select to **Open a local folder**.
  This is the correct way to make Visual Studio directly handle
  **CMake** projects.

- Select the build root folder.

- Ensure `x64-Debug` build configuration is selected. This will start
  the cmake configuration process and building of vcpkg dependencies.
  Note: You can see the progress by opening the Output window (if not
  already open) by navigating to View → Output

<div class="note">

This step will take a long time (several hours, depending on the speed
of your computer). It is recommended to run this step overnight. It
might appear as at some points the process is stuck because the output
window doesn’t update for many minutes (or even an hour, depending on
your machine) with a message like `Building x64-windows`. This is nomal.
Keep visual studio open until it is complete.

</div>

- Wait until the "CMake in Visual Studio" tab appears and the CMake
  process hits an error, which we’ll fix in the next step.

### 3. Configure paths to SWIG

SWIG must be installed manually. Obtain the latest `swigwin` package
from <https://sourceforge.net/projects/swig/files/swigwin/> (at the time
of this writing, the latest is `swigwin-4.1.1`) and extract the zip file
to a known location.

At present, you must add CMake arguments SWIG that you downloaded in a
previous step. Choose the Configuration you want to use (e.g. x64-Debug)
in the toolbar. Then, open the CMake Settings editor (Project menu \>
CMake Settings), and add the following items to the CMake Command
Arguments option field:

`-DSWIG_EXECUTABLE=C:\\PATH\\TO\\swigwin-4.1.1\\swig.exe`

Replace the `PATH\\TO\\` section with the appropriate paths to SWIG on
your installation. Make sure to change single backslashes to double
backslashes if you are pasting in a path from Windows Explorer.

Save the changes. CMake configuration should now succeed, showing "CMake
generation finished" in the output window at the bottom. You can now
build! (Build menu \> Build all)

### 4. Running and debugging

Running or debugging KiCad directly from the build directory through
Visual Studio requires that you set certain environment variables so
that KiCad can find runtime dependencies and Python works. To configure
the runtime environment for programs run from Visual Studio, edit the
`launch.vs.json` file in the project root directory. To open this file,
select Debug and Launch Settings for kicad from the Debug menu.

<div class="note">

If you have multiple launch configurations, for example to debug
standalone pcbnew or eeschema in addition to kicad, you will need to
configure each one separately.

</div>

The variable `KICAD_RUN_FROM_BUILD_DIR` must be present (it can be set
to any value) to run from the build directory. Setting this variable
allows KiCad to find dynamic libraries when running from the build
directory.

The variable `KICAD_USE_EXTERNAL_PYTHONHOME` must be present (it can be
set to any value) when running from either the build or the install
directory, which allows you to specify `PYTHONHOME` in the environment.
Normally, KiCad on Windows uses a Python that is bundled and placed in a
specific location by the installer, and when running from Visual Studio,
Python will be in a different location.

The variable `PYTHONHOME` must be present and set to the location of the
Python installed by vcpkg.

The variable `PYTHONPATH` must be present and set to include the paths
to the `pcbnew` python module and the `kicad_pyshell` module. The former
is built next to `pcbnew.exe` in the build directory, and the latter
lives in the source tree in the `scripting` directory.

Use the `env` section of each configuration entry to set the required
environment variables, including the path to the `vcpkg` installed
packages directory, which will allow KiCad to find dynamic libraries
from dependencies. For example, the below configuration allows running
and debugging `kicad.exe` from the build directory.

Create this file as `<kicad_source>/.vs/launch.vs.json` to have a
baseline/working default launch configuration:

    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "kicad.exe (kicad\\kicad.exe)",
          "name": "kicad.exe (kicad\\kicad.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "pcbnew.exe (pcbnew\\pcbnew.exe)",
          "name": "pcbnew.exe (pcbnew\\pcbnew.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "eeschema.exe (eeschema\\eeschema.exe)",
          "name": "eeschema.exe (eeschema\\eeschema.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "gerbview.exe (gerbview\\gerbview.exe)",
          "name": "gerbview.exe (gerbview\\gerbview.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "pl_editor.exe (pagelayout_editor\\pl_editor.exe)",
          "name": "pl_editor.exe (pagelayout_editor\\pl_editor.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "pcb_calculator.exe (pcb_calculator\\pcb_calculator.exe)",
          "name": "pcb_calculator.exe (pcb_calculator\\pcb_calculator.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        },
        {
          "type": "default",
          "project": "CMakeLists.txt",
          "projectTarget": "bitmap2component.exe (bitmap2component\\bitmap2component.exe)",
          "name": "bitmap2component.exe (bitmap2component\\bitmap2component.exe)",
          "env": {
            "KICAD_RUN_FROM_BUILD_DIR": "1",
            "KICAD_USE_EXTERNAL_PYTHONHOME": "1",
            "PYTHONHOME": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\tools\\python3",
            "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
            "PATH": "${cmake.buildRoot}\\vcpkg_installed\\x64-windows\\debug\\bin;${cmake.buildRoot}\\common;${cmake.buildRoot}\\api;${cmake.buildRoot}\\common\\gal;${env.PATH}"
          }
        }
      ]
    }

## Visual Studio Extras

### Trailing Whitespace Remover (Extension)

It is **highly recommended** users install the [Trailing Whitespace
Visualizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.TrailingWhitespace64)
for Visual Studio 2022 which will not only highlight trailing whitespace
as you type but also automatically remove it by default when you save
the file.

### natvis definitions for libraries

Visual Studio supports defining decoders for objects in debug views.

You can find some useful ones here:

- <https://github.com/wxWidgets/wxWidgets/blob/master/misc/msvc/wxWidgets.natvis>

- <https://github.com/nlohmann/json/blob/develop/nlohmann_json.natvis>

- <https://github.com/Open-Cascade-SAS/OCCT/blob/master/dox/debug/occt.natvis>

Simply download the files and drop them into:
`%USERPROFILE%\Documents\Visual Studio 2022\Visualizers`

VS will load them after it starts up.

## Advanced

<div class="warning">

It is recommended to only try these changes after getting a basic
configuration working using the above steps.

</div>

### Binary caching

By default vcpkg will bundle up each dependency and store it in a
**binary cache** which maintains copies of all past built dependencies
by version.

The binary cache is located usually in %LOCALAPPDATA%\vcpkg\archives

If storage space consumed is a problem.

You may change the location of the binary cache by setting the
environment variable `VCPKG_DEFAULT_BINARY_CACHE` to a different path.

or

You may disable binary caching by setting the environment variable
`VCPKG_FEATURE_FLAGS` with value `-binarycaching`. This is not advisable
as the intention of the cache is to avoid rebuilds if the application
cmake cache is destroyed and rebuilt and rebuilding kicad dependencies
is quite time consuming.

### Manifest mode

The KiCad repository is configured to use [Manifest
Mode](https://learn.microsoft.com/en-us/vcpkg/users/manifests).

The benefits of using this is that it ensures the developer’s
dependencies always match that of the project so that if any
dependencies are added or version bumped, they will be automatically
build. The negative side of manifest mode is that whenever you update
your version of visual studio or navigate the git history, you will need
to rebuild vcpkg dependencies.

If this is deemed undesirable, it is possible to disable manifest mode
locally by following these steps:

1.  Copy vcpkg-configuration.json from kicad root into your vcpkg root

2.  Manually run a vcpkg install command for all dependencies currently
    defined in the KiCad root `vcpkg.json`. E.g. something like below:

        .\vcpkg.exe install --recurse --triplet x64-windows boost-algorithm boost-bimap boost-filesystem boost-functional boost-iterator boost-locale boost-optional boost-property-tree boost-ptr-container boost-range boost-test boost-uuid cairo wxwidgets glew curl gettext[tools] harfbuzz glm opencascade[rapidjson] opengl python3 openssl sqlite3[fts5,fts4,fts3,rtree,session] icu ngspice wxpython libgit2[ssh,winhttp] nng protobuf zstd

3.  .\vcpkg upgrade --no-dry-run

4.  Set cmake variable `VCPKG_MANIFEST_MODE` to `OFF`

5.  Ensure launch.vs.json `PATH` and `PYTHONHOME` variables point to the
    vcpkg folder (instead of the one in cmake root) - i.e. modify to be
    as follows:

                "PYTHONHOME": "C:\\PATH\\TO\\vcpkg_installed\\x64-windows\\tools\\python3",
                "PYTHONPATH": "${cmake.buildRoot}\\pcbnew;${projectDir}\\scripting",
                "PATH": "C:\\PATH\\TO\\vcpkg\\installed\\x64-windows\\debug\\bin;${env.PATH}",

6.  Delete cmake cache and reconfigure

<div class="warning">

Disabling manifest mode means you have to manually ensure that the
dependencies you have installed locally match those required by the
KiCad project.

</div>

## Troubleshooting

### vcpkg cannot finish installing a dependency

Antivirus software is known to block interim steps in the package build
process. Try temporarily disabling your antivirus or adding an
exception.

### Error: Couldn’t find the versions database file

If this occurs, a mismatch between vcpkg and registries occurred when it
was checking your already installed libraries within the kicad build
repo. The easiest fix is to simply `Delete Cache and Reconfigure` under
the Project menu option
