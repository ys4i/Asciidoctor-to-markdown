title: Build Options weight: 17 summary: Summary of CMake build
configuration options. ---

These are the build options that can be passed to CMake during
configuration.

# All Platforms

| Option                      | Description                                                                                                                                                                                                                                                                                                                                                     | Default |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| KICAD_SCRIPTING_WXPYTHON    | Build wxPython implementation for wx interface building in Python and py.shell.                                                                                                                                                                                                                                                                                 | ON      |
| KICAD_INSTALL_DEMOS         | Install KiCad demos and examples.                                                                                                                                                                                                                                                                                                                               | ON      |
| KICAD_BUILD_QA_TESTS        | Build software Quality assurance unit tests.                                                                                                                                                                                                                                                                                                                    | ON      |
| KICAD_SPICE                 | Build KiCad with internal Spice simulator.                                                                                                                                                                                                                                                                                                                      | ON      |
| KICAD_BUILD_I18N            | Build the translation language libraries                                                                                                                                                                                                                                                                                                                        | OFF     |
| KICAD_I18N_UNIX_STRICT_PATH | Install the language libraries to the standard UNIX install path of \${CMAKE_INSTALL_PREFIX}/share/locale                                                                                                                                                                                                                                                       | OFF     |
| BUILD_SMALL_DEBUG_FILES     | In debug build: create smaller binaries. On Windows, binaries created by link option -g3 are **very large** (more than 1Gb for Pcbnew, and more than 3Gb for the full KiCad suite). This option create binaries using link option -g1 that create much smaller files, but there are less info in debug (However the file names and line numbers are available). | OFF     |
| MAINTAIN_PNGS               | Allow build/rebuild bitmap icons used in menus from the corresponding .svg file. Set to true if you are a PNG maintainer and have the required tools given in the bitmaps_png/CMakeLists.txt file.                                                                                                                                                              | OFF     |
| KICAD_IPC_API               | Build KiCad with support for the IPC API server (requires the `nng` library)                                                                                                                                                                                                                                                                                    | ON      |

# Platform Specific

| Option                       | Description                                                                 | Default |
|------------------------------|-----------------------------------------------------------------------------|---------|
| KICAD_WIN32_DPI_AWARE        | Turn on DPI awareness for Windows builds only.                              | OFF     |
| KICAD_WIN32_CONTEXT_WINFIBER | Use win32 fibers for libcontext.                                            | OFF     |
| KICAD_USE_EGL                | Build KiCad with EGL backend support for Wayland.                           | OFF     |
| KICAD_USE_BUNDLED_GLEW       | Use the bundled version of GLEW - only available when KICAD_USE_EGL is set. | OFF     |

# Developer Specific

<table>
<colgroup>
<col style="width: 23%" />
<col style="width: 70%" />
<col style="width: 5%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">Option</th>
<th style="text-align: left;">Description</th>
<th style="text-align: left;">Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_SANITIZE_ADDRESS</p></td>
<td style="text-align: left;"><p>Build KiCad with address sanitizer
(ASan) options.<br />
WARNING: May cause errors with Gold, Lld, Mold linkers.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_SANITIZE_THREADS</p></td>
<td style="text-align: left;"><p>Build KiCad with thread sanitizer
(TSan) options.<br />
WARNING: May cause errors with Gold, Lld, Mold linkers.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_STDLIB_DEBUG</p></td>
<td style="text-align: left;"><p>Build KiCad with libstdc++ debug flags
enabled.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_STDLIB_LIGHT_DEBUG</p></td>
<td style="text-align: left;"><p>Build KiCad with libstdc++ with
-Wp,-D_GLIBCXX_ASSERTIONS flag enabled.<br />
Not as intrusive as KICAD_STDLIB_DEBUG</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_DRC_PROTO</p></td>
<td style="text-align: left;"><p>Build the DRC prototype QA
tool.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_BUILD_PNS_DEBUG_TOOL</p></td>
<td style="text-align: left;"><p>Build the P&amp;S debugging/playground
QA tool.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_GAL_PROFILE</p></td>
<td style="text-align: left;"><p>Enable profiling info for GAL.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_USE_VALGRIND</p></td>
<td style="text-align: left;"><p>Build KiCad with valgrind stack
tracking enabled.</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
</tbody>
</table>

# Notes

## Note 1

Python 3 is required to build KiCad. The path to Python is normally
determined automatically by a CMake script, but if needed,
`PYTHON_EXECUTABLE` can be defined when invoking cmake ( use
`-DPYTHON_EXECUTABLE=<python path>` ) to specify a particular Python
binary.

## Note 2

These Symbols are always defined, and are not an option for cmake
invocation:

**COMPILING_DLL**

This is a signal to import_export.h, and when present, toggles the
interpretation of the \#defines in that file. Its purpose should not be
extended beyond this.

**USE_KIWAY_DLLS**

Comes from CMake as a user configuration variable, settable in the Cmake
user interface. It decides if KiCad will be built with the \*.kiface
program modules.

**BUILD_KIWAY_DLL**

Comes from CMake, but at the 2nd tier, not the top tier. By 2nd tier,
something like pcbnew/CMakeLists.txt, not /CMakeLists.txt is meant. It
is not a user configuration variable. Instead, the 2nd tier
CMakeLists.txt file looks at the top level `USE_KIWAY_DLLS` and decides
how the object files under the 2nd tier’s control will be built. If it
decides it wants to march in lockstep with `USE_KIWAY_DLLS`, then this
local CMakeLists.txt file may pass a defined `BUILD_KIWAY_DLL`
(singular) on the compiler command line to the pertinent set of
compilation steps under its control.

## Note 3

When building with `KICAD_BUILD_I18N` on Linux systems, gettext needs
the rule files `shared-mime-info.its` and `metainfo.its`/`appdata.its`
to translate the Linux metadata files.
