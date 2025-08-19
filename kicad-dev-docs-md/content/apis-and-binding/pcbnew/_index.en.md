date: 2016-04-09T16:50:16+02:00 title: PCB Python Bindings weight: 18
---

# Deprecation notice

<div class="warning">

The SWIG-based Python bindings in KiCad are deprecated as of KiCad 9.0
and will be removed in a future version. They will be replaced by the
[IPC API](../ipc-api/), which is a stable interface that is accessible
from many languages including Python. The current plan is to remove the
SWIG bindings in KiCad 10.0. This plan is subject to delay based on
development timelines and community feedback. KiCad 10.0 is currently
planned for release in February 2026.

</div>

Check out the [kicad-python
library](https://gitlab.com/kicad/code/kicad-python) for a Pythonic
interface to the IPC API.

# Introduction

KiCad implements a Python plugin interface so that external Python
plugins can be run from within the PCB editor. The interface is
generated using the `Simplified Wrapper and Interface Generator` or
[SWIG](http://www.swig.org). SWIG is instructed to translate specific
C/C++ header files into other languages using `interface` files. These
files ultimately decide what C/C++ functions, classes and other
declarations are exported and can be found in **pcbnew/swig/**.

During build-time the SWIG interface files are used to generate the
corresponding .py files. These files are installed into Python’s
system-wide **dist-packages** repository, thus they can be imported by
any Python 3 interpreter installed on the system.

<div class="warning">

Since this interface is a raw binding layer over KiCad rather than a
true API, it is unstable and will generally change between KiCad major
versions. While the development team will make reasonable efforts to
maintain compatiblity of function, there are no guarantees on strict
compatibility, so plugins will need to handle situations such as
function calls and data types being renamed from time to time.

</div>

Python bindings are provided for the PCB editor only at this time. Using
Python from within the other parts of KiCad is not supported. The Python
binding module is called `pcbnew`, a reference to the internal name of
the PCB Editor (which was its official name in old versions of KiCad).

# Existing PCB Editor Python Bindings documentation

The PCB Editor Python bindings can be used stand-alone, i.e. no instance
of the PCB Editor is running and the board project to be manipulated is
loaded and saved from and to file. This approach is shown with some
examples in the [user’s
documentation](https://docs.kicad.org/master/en/pcbnew/pcbnew.html#scripting).

Another documentation source is the auto-generated Doxygen reference of
the bindings. It can be found here:

- [KiCad
  6.0](http://docs.kicad.org/doxygen-python-6.0/namespacepcbnew.html)

- [KiCad
  7.0](http://docs.kicad.org/doxygen-python-7.0/namespacepcbnew.html)

- [KiCad
  8.0](http://docs.kicad.org/doxygen-python-8.0/namespacepcbnew.html)

- [KiCad
  Nightly](http://docs.kicad.org/doxygen-python-nightly/namespacepcbnew.html)

# "Action Plugin" Support

Besides the stand-alone usage of the generated Python plugin interface,
additional support regarding online manipulation of board projects is
available for the PCB Editor. Plugins using this feature are called
**Action Plugins** and they are accessible using a PCB Editor menu entry
that can be found under **Tools→External Plugins**. KiCad plugins that
follow the **Action Plugin** conventions can be made to show up as
external plugins in that menu and optionally as top toolbar button. The
user can run the plugin resulting in calling a defined entry function in
the Python plugin’s code. This function can then be used to access and
manipulate the currently loaded board from the Python script
environment.

## Typical Plugin Structure

The **Action Plugin** support is implemented in the PCB editor by
discovering Python packages and Python script files in specific
directories on startup. In order for the discovery process to work, the
following requirements must be met.

- The plugin must be installed in the KiCad plugins search paths as
  documented in `scripting/kicadplugins.i`. You can always discover the
  search path for your setup by opening the scripting console and
  entering the command:

`import pcbnew; print(pcbnew.PLUGIN_DIRECTORIES_SEARCH)`

Currently on a Linux Installation the plugins search path is

- `` /usr/share/kicad/scripting/plugins/` ``

- `~/.kicad/scripting/plugins`

- `` ~/.kicad_plugins/` ``

On Windows

- `%KICAD_INSTALL_PATH%/share/kicad/scripting/plugins`

- `%APPDATA%/Roaming/kicad/scripting/plugins`

On macOS, there is a security feature that makes it easier to add
scripting plugins to the ~/Library…​ path than to kicad.app, but the
search path is

- `/Applications/kicad/Kicad/Contents/SharedSupport/scripting/plugins`

- `~/Library/Application Support/kicad/scripting/plugins`

- Alternatively a symbolic link can be created in the KiCad plugin path
  link to the plugin file/folder in another location of the file system.
  This can be useful for development.

- The plugin must be written as a simple Python script (\*.py) located
  in the plugin search path. Note that this method is preferred for
  small plugins consisting of a single .py file.

- Alternatively the plugin must be implemented as a Python package
  conforming to the Python package standard definitions (See [6.4.
  Packages](https://docs.python.org/2/tutorial/modules.html#packages).
  Note that this method is preferred for larger plugin projects
  consisting of multiple .py files and resource files such as dialogs or
  images.

- The Python plugin must contain a class derived from
  `pcbnew.ActionPlugin` and it’s `register()` method must be called
  within the plugin.

The following examples demonstrate the plugin requirements.

## Simple Plugin Example

The folder structure of the simple plugin is fairly straight forward. A
single Python script file is placed into a directory that is present in
the KiCad plugin path.

    + ~/.kicad_plugins/ # A folder in the KiCad plugin path
        - simple_plugin.py
        - simple_plugin.png (optional)

The file `simple_plugin.py` contains the following.

``` python
import pcbnew
import os

class SimplePlugin(pcbnew.ActionPlugin):
    def defaults(self):
        self.name = "Plugin Name as shown in Pcbnew: Tools->External Plugins"
        self.category = "A descriptive category name"
        self.description = "A description of the plugin and what it does"
        self.show_toolbar_button = False # Optional, defaults to False
        self.icon_file_name = os.path.join(os.path.dirname(__file__), 'simple_plugin.png') # Optional, defaults to ""

    def Run(self):
        # The entry function of the plugin that is executed on user action
        print("Hello World")

SimplePlugin().register() # Instantiate and register to Pcbnew
```

Note that if specified `icon_file_name` must contain absolute path to
the plugin icon. It must be png file, recommended size is 24x24 pixels.
Alpha channel for opacity is supported. If icon is not specified a
generic tool icon will be used.

An additional property `self.dark_icon_file_name` may be specified to
provide an alternate icon for dark window themes. If this property is
not present, the icon specified in `icon_file_name` will be used when
the dark theme is active;

`show_toolbar_button` only defines a default state for plugin toolbar
button. Users can override it in the PCB Editor preferences.

## Complex Plugin Example

The complex plugin example represents a single Python package that is
imported on startup of the editor. When the Python package is imported,
the `__init__.py` file is executed and is thus a perfect place to
instantiate and register the plugin to KiCad. The big advantage here is,
that you can modularize your plugin much better and include other files
without cluttering the KiCad plugin directory. Additionally, the same
plugin can be executed standalone using `python -m` e.g. to perform
tests on the Python code. The following folder structure shows how
complex plugins are implemented:

    + ~/.kicad_plugins/ # this directory has to be in the plugin path
        + complex_plugin/ # The plugin directory (A Python package)
            - __init__.py # This file is executed when the package is imported (on PCB editor startup)
            - __main__.py # This file is optional. See below
            - complex_plugin_action.py # The ActionPlugin derived class lives here
            - complex_plugin_utils.py # Other Python parts of the plugin
            - icon.png
            + otherstuff/
                - otherfile.png
                - misc.txt

It is recommended to name the file containing the ActionPlugin derived
class as `<package-name>_action.py`. In this case the file is named
`complex_plugin_action.py` with the following contents:

``` python
import pcbnew
import os

class ComplexPluginAction(pcbnew.ActionPlugin):
    def defaults(self):
        self.name = "A complex action plugin"
        self.category = "A descriptive category name"
        self.description = "A description of the plugin"
        self.show_toolbar_button = True # Optional, defaults to False
        self.icon_file_name = os.path.join(os.path.dirname(__file__), 'icon.png') # Optional

    def Run(self):
        # The entry function of the plugin that is executed on user action
        print("Hello World")
```

The `__init__.py` file is then used to instantiate and register the
plugin to Pcbnew as follows.

``` python
from .complex_plugin_action import ComplexPluginAction # Note the relative import!
ComplexPluginAction().register() # Instantiate and register to PCB editor
```

As described in [PEP 338](https://www.python.org/dev/peps/pep-0338/)
Python can execute packages (or modules) as scripts. This can be useful
to implement a command-line stand-alone version of your KiCad plugin
with minimum effort. In order to implement this feature, a `__main__.py`
file is created in the package directory. This file can be executed by
running the following command.

    python -m <package_name>

Make sure that your current directory is the parent directory of the
package directory when running the command. In these examples, this
would be `~/.kicad_plugins`. When running the command the Python
interpreter runs `/complex_plugin/__init__.py` followed by
`/complex_plugin/__main__.py`.
