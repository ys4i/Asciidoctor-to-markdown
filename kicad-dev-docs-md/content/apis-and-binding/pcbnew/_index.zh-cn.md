date: 2016-04-09T16:50:16+02:00 title: PcbNew 插件 weight: 18 ---

KiCad 实现了一个 Python 插件接口，因此外部 Python 插件可以从 Pcbnew
内部运行。 使用 `简化的包装器和接口生成器` 或
[SWIG](http://www.swig.org) 生成接口。 SWIG 被指令使用 `interface`
文件将特定的 C/C 头文件翻译成其他语言。 这些文件最终决定导出哪些 C/C
函数、类和其他声明， 这些声明可以在 **pcbnew/swg/** 中找到。

在构建时，SWIG 界面文件用于生成相应的 .py 文件。 这些文件被安装到 Python
的系统级 **dist-Packages** 库中， 因此它们可以由系统上安装的任何 Python3
解释器导入。

# 现有 Pcbnew Python API 文档

Pcbnew Python API 可以独立使用，即没有 Pcbnew 实例在运行，
要操作的电路板工程被加载并保存到文件中。 此方法通过
[用户文档](https://docs.kicad.org/master/en/pcbnew/pcbnew.html##scripting)
中的一些示例进行了说明。

另一个文档源是 API 的自动生成的 DOORT 参考。 它可以在
[这里](http://docs.kicad.org/doxygen-python/namespacepcbnew.html) 找到。

# "动作插件" 支持

除了独立使用生成的 Python 插件界面外，Pcbnew
还提供了关于在线操作电路板工程的额外支持。 使用此功能的插件称为
**动作插件**，可以使用 Pcbnew 菜单项访问它们，该菜单项可以在 **工具 →
外部插件** 下找到。 遵循 **动作插件** 约定的 KiCad
插件可以在该菜单中显示为外部插件，也可以选择显示为顶部工具栏按钮。
用户可以运行该插件，从而调用 Python 插件代码中定义的 Entry 函数。
然后可以使用此函数从 Python 脚本环境访问和操作当前加载的电路板。

## 典型的插件结构

**动作插件** 支持在 Pcbnew 中通过在启动时发现特定目录中的 Python 包和
Python 脚本文件来实现。 要使发现过程正常工作，必须满足以下要求。

- 插件必须安装在 KiCad 插件搜索路径中，如 `scriting/kicadplugins.i`
  中所述。
  您始终可以通过打开脚本控制台并输入以下命令来查找设置的搜索路径：

`import pcbnew; print pcbnew.PLUGIN_DIRECTORIES_SEARCH`

当前在 Linux 安装上，插件搜索路径为

- `` /usr/share/kicad/scripting/plugins/` ``

- `~/.kicad/scripting/plugins`

- `` ~/.kicad_plugins/` ``

在 Windows 上

- `%KICAD_INSTALL_PATH%/share/kicad/scripting/plugins`

- `%APPDATA%/Roaming/kicad/scripting/plugins`

在 macOS 上，有一个安全特性，可以更容易地将脚本插件添加到 ~/Library…​
路径比到 kicad.app 方便， 但搜索路径是

- `/Applications/kicad/Kicad/Contents/SharedSupport/scripting/plugins`

- `~/Library/Application Support/kicad/scripting/plugins`

- 或者，可以在 KiCad
  插件路径链接中创建指向文件系统其他位置的插件文件/文件夹的符号链接。这对开发很有用。

- 插件必须编写为位于插件搜索路径中的简单 Python 脚本 (\*.py)。
  请注意，这种方法是由单个 .py 文件组成的小型插件的首选方法。

- 或者，必须将插件实现为符合 Python 包标准定义的 Python 包 (请参见
  [6.4.包](https://docs.python.org/2/tutorial/modules.html#packages))。
  请注意，此方法是由多个 .py 文件和资源文件 (如对话框或图像)
  组成的较大插件项目的首选方法。

- Python 插件必须包含一个派生自 `pcbnew.ActionPlugin` 的类，并且它的
  `register()` 方法必须在插件内调用。

以下示例演示了插件要求。

## 简单插件示例

这个简单插件的文件夹结构相当简单。 单个 Python 脚本文件放置在 KiCad
插件路径中的目录中。

    + ~/.kicad_plugins/ # KiCad 插件路径中的文件夹
        - simple_plugin.py
        - simple_plugin.png (可选)

`simple_plugin.py` 文件包含以下内容。

``` python
import pcbnew
import os

class SimplePlugin(pcbnew.ActionPlugin):
    def defaults(self):
        self.name = "插件名称，如 Pcbnew：Tools->External Plugins 中所示"
        self.category = "描述性类别名称"
        self.description = "对插件及其功能的描述"
        self.show_toolbar_button = False # 可选，默认为 False
        self.icon_file_name = os.path.join(os.path.dirname(__file__), 'simple_plugin.png') # 可选，默认为 ""

    def Run(self):
        # 在用户操作时执行的插件的入口函数
        print("Hello World")

SimplePlugin().register() # 实例化并注册到 Pcbnew
```

请注意，如果指定的 `icon_file_name` 必须包含插件图标的绝对路径。 必须是
PNG 文件，建议大小为 26x26 像素。 支持不透明度的 Alpha 通道。
如果未指定图标，将使用通用工具图标

`show_toolbar_button` 只定义了插件工具栏按钮的默认状态。 用户可以在
pcbnew 首选项中覆盖它。

## 复杂插件示例

复杂插件示例表示在 Pcbnew 启动时导入的单个 Python 包。 在导入 Python
包时，会执行 `init.py` 文件， 因此非常适合实例化插件并将其注册到
Pcbnew。 这里最大的优势是，您可以更好地模块化您的插件并包含其他文件，
而不会使 KiCad 插件目录变得杂乱无章。 此外，可以使用 `python -m`
独立执行相同的插件。 例如对 Python 代码执行测试。
以下文件夹结构显示了复杂插件的实现方式：

    + ~/.kicad_plugins/ # 此目录必须位于插件路径中
        + complex_plugin/ # 插件目录 (Python 包)
            - __init__.py # 此文件在导入软件包时执行 (在 Pcbnew 启动时)
            - __main__.py # 此文件是可选的。见下文
            - complex_plugin_action.py # ActionPlugin 派生类位于此处
            - complex_plugin_utils.py # 插件的其他 Python 部分
            - icon.png
            + otherstuff/
                - otherfile.png
                - misc.txt

建议将包含 ActionPlugin 派生类的文件命名为 `<package-name>_action.py`。
在本例中，文件名为 `complex_plugin_action.py`，内容如下：

``` python
import pcbnew
import os

class ComplexPluginAction(pcbnew.ActionPlugin):
    def defaults(self):
        self.name = "一个复杂的动作插件"
        self.category = "描述性类别名称"
        self.description = "该插件的说明"
        self.show_toolbar_button = True # 可选，默认为 False
        self.icon_file_name = os.path.join(os.path.dirname(__file__), 'icon.png') # 可选

    def Run(self):
        # 在用户操作时执行的插件的入口函数
        print("Hello World")
```

然后使用 `init.py` 文件实例化插件并将其注册到 Pcbnew，如下所示。

``` python
from .complex_plugin_action import ComplexPluginAction # 注意相对的导入！
ComplexPluginAction().register() # 实例化并注册到 Pcbnew
```

正如 Python [PEP 338](https://www.python.org/dev/peps/pep-0338/)
中所述可以将包 (或模块) 作为脚本执行。 这对于以最小的工作量实现 KiCad
插件的命令行独立版本非常有用。
为了实现这一功能，我们在包目录下创建了一个 `main.py` 文件。
可以通过运行以下命令来执行此文件。

    python -m <package_name>

运行该命令时，请确保您的当前目录是软件包目录的父目录。
在这些示例中，这将是 `~/.kicad_plugins`。 运行该命令时，Python
解释器会运行 `/complex_plugin/init.py` 后跟 `/complex_plugin/main.py`.
