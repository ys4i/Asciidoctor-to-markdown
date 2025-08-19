date: 2024-02-06T10:30:00+10:00 title: For Add-on Developers weight: 17
---

# API Documentation for Add-on Developers

The KiCad IPC API is a new feature in KiCad 9.0 that allows third-party
applications to interact with KiCad. Unlike the SWIG-based Python
bindings to KiCad’s PCB editor, the IPC API is designed to be a stable
interface that does not change when KiCad’s internals are refactored,
and does not require that KiCad build against Python directly. It is
also designed to be language-agnostic, so that interoperability with
software written in languages other than Python is possible.

## API Status and Goals

The initial release of the IPC API in KiCad 9.0 is focused on a
particular set of use cases that will allow many (but not all) of the
things that people have built using the SWIG-based Python bindings to be
done with the new API. The API will be expanded over time (and, when
practical, additions will be backported to the 9.0 branch).

There are a few important limitations to be aware of when considering
use of the IPC API:

1.  The IPC API currently only supports communication with a running
    instance of the KiCad GUI. There is no support for running KiCad in
    a headless mode, and unlike the SWIG-based Python bindings, the IPC
    API does not provide an independent way to load and process KiCad
    PCB files outside of the KiCad editor. In the future, a headless
    mode (implemented through `kicad-cli`) will support third-party
    tools that want a way to interact with KiCad design files without
    running the full KiCad GUI. Even then, third-party tools will need
    to launch `kicad-cli` and then communicate with it. There are no
    plans to support a standalone library for loading and manipulating
    KiCad files separate from the client-server (IPC) model.

2.  The IPC API currently has no support for plotting or exporting files
    from KiCad designs. This support may be added in the future, however
    the primary way we expect users to export files and generate other
    outputs from KiCad design files in an automated way is through the
    use of the `kicad-cli` tool. If you are currently relying on Python
    scripting for generating outputs from KiCad, we recommend moving to
    `kicad-cli`.

In KiCad 9.0, the IPC API and the new IPC plugin system are only
implemented in the PCB editor, due to development time constraints. In
the future, the IPC API will be expanded to support the schematic
editor, library editors, and other parts of KiCad. While there are
specific places where the IPC API has new features that are not
available in the SWIG-based Python bindings, in general, the
capabilities of the IPC API in KiCad 9.0 should be seen as equivalent to
the Action Plugins system present since KiCad 5: a way to interact with
a running KiCad PCB Editor session.

The API is still under active development, although after KiCad 9.0.0 is
released, existing API messages will be treated as stable and will be
supported according to our deprecation policy. We encourage developers
to report issues and request new capabilities as they experiment with
the API.

## Connecting to KiCad

API clients such as scripts and plugins connect to KiCad using a Unix
domain socket on macOS and Linux, and a named pipe on Windows. The
socket or pipe is created by KiCad in the user’s temporary directory
when KiCad is launched. The name of the socket or pipe is `api.sock` by
default, but multiple instances of KiCad can be run simultaneously, and
if this name is already taken, KiCad will append its process id number
(PID) to the name to make it unique.

When KiCad launches API plugins (see below), it will set several
environment variables that can be used by the client to know where to
connect to the IPC API. These variables include:

- `KICAD_API_SOCKET`: The full path to the socket or pipe that the
  client should connect to.

- `KICAD_API_TOKEN`: A generated token that the client should use in
  request messages. This token is generally passed to the API client
  library when creating a connection to KiCad (for example, in the
  `init` method of a `KiCad` object in `kicad-python`). This token is
  unique to the running instance of KiCad, and can be used by
  long-running clients to detect if KiCad restarts in the middle of a
  session.

API clients do not need to be launched from KiCad, but if they are not
(for example, if a client is launched from an IDE while developing a
plugin), the client will not have access to these environment variables.
For this reason, it is recommended that plugin developers run only one
instance of KiCad at a time while developing and testing plugins.

## Plugin Action registration

IPC plugins can register actions that appear on the PCB editor’s toolbar
much like the SWIG-based Action Plugins system. There are several
differences in how this is done, however:

1.  The default directory for IPC plugins is
    `${KICAD_DOCUMENTS_HOME}/<version>/plugins`, where
    `${KICAD_DOCUMENTS_HOME}` is the root path for KiCad’s user data
    directory, which is platform-dependent. On Windows, it is typically
    `C:\Users\<username>\Documents\KiCad`, on macOS it is typically
    `/Users/<username>/Documents/KiCad`, and on Linux it is typically
    `~/.local/share/KiCad`.

2.  Plugins must provide a `plugin.json` file containing metadata about
    the plugin and any actions it provides. The `plugin.json` file must
    be in a subdirectory of the plugins directory, and must follow the
    [schema defined by KiCad](https://go.kicad.org/api/schemas/v1).

The plugin system currently supports two types of plugins: Python-based,
and executable. Python plugins are run by an external Python interpreter
(unlike the SWIG-based plugins which run in an interpreter embedded into
KiCad), and KiCad will automatically manage the creation of a virtual
environment for each plugin using the chosen Python interpreter. On
macOS and Windows, KiCad will continue to ship with its own Python
interpreter, which will be used by the IPC system by default if the user
does not specify a different interpreter. On Linux, KiCad uses the first
version of Python found in the user’s PATH by default. Executable
plugins are run as separate processes with an arbitrary command line,
and can be used to launch plugins written in compiled languages, or any
other kind of external tool that does not require a Python environment.

## Debugging

There are a few ways to get debug information from KiCad that might be
useful while developing against the IPC API, including trace output from
KiCad as well as a log of all API requests and responses.

To enable API-related trace output from KiCad itself (which is
particularly useful for debugging Python plugins that are being loaded
by KiCad), set the following environment variables before launching
KiCad:

``` sh
KICAD_ALLOC_CONSOLE=1   # If you are on Windows, this variable is required to show any output
KICAD_ENABLE_WXTRACE=1  # This enables KiCad's tracing system even when running a release build
WXTRACE=KICAD_API       # This enables trace output for the API subsystem
```

To enable the API log file, create an advanced config file in your KiCad
[configuration
directory](https://docs.kicad.org/8.0/en/kicad/kicad.html#settings) if
you don’t have one already (a plain text file named `kicad_advanced`),
and add the following line:

    EnableAPILogging=1

This will create a file at
`${KICAD_DOCUMENTS_HOME}/<version>/logs/api.log` containing every API
request and response at the Protobuf message level.

<div class="note">

The API log file can grow quite large, so be sure to disable it when you
are done debugging.

</div>

Plugins using the Python runtime are launched from a virtual environment
created by KiCad for that plugin. You can find the virtual environment
for each plugin located in a cache folder
`${KICAD_CACHE_HOME}/python-environments/<plugin_identifier>`. The
default `KICAD_CACHE_HOME` is platform-dependent:
`~/Library/Caches/KiCad/<version>` on macOS,
`C:\Users\<username>\AppData\Local\KiCad\<version>` on Windows, and
`~/.cache/KiCad/<version>` on most Linux systems. Activating this
virtual environment will allow you to run the plugin from a terminal or
IDE, manually install Python packages, and debug the plugin using the
Python debugger.

## Python Bindings

`kicad-python` is the official Python bindings library for the IPC API.
It is a wrapper around the Protobuf-based API that exposes Python
classes and helper methods that make it easier to interact with the IPC
API from Python.

The `kicad-python` library is available on
[PyPI](https://pypi.org/project/kicad-python/).

Automatically-generated documentation for `kicad-python` is available
online at <https://docs.kicad.org/kicad-python-main/>.

There are some examples of scripts and plugins in the `examples`
directory of the `kicad-python` repository that may be a good starting
point for developing your own tools.
