title: 入门 weight: 10 ---

# 开发工具

在开始编译 KiCad 之前，除了编译器之外，还需要一些工具。
这些工具中有些是从源代码编译所必需的，有些是可选的。

## CMake 编译配置工具

[CMake](https://cmake.org) 是 KiCad 使用的编译配置和 Makefile
生成工具。这是必需的。

## Git 版本控制系统

官方的源代码仓库托管在 [GitLab](https://gitlab.com/) 上，需要
[git](https://git-scm.com/) 获取最新的源代码。 如果你更喜欢使用
[GitHub](https://github.com/)，有一个官方 KiCad 仓库的只读镜像。
以前的官方托管地点 [Launchpad](https://launchpad.net/kicad/)
仍作为镜像活跃。 更改应作为
[合并请求](https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html)
通过 GitLab 提交。 开发团队不会审查在 GitHub 或 Launchpad
上提交的更改，因为这些平台只是镜像。

## Doxygen 代码文档生成器

KiCad 源代码使用 [Doxygen](https://www.doxygen.nl/index.html) 解析 KiCad
源代码文件，并将依赖关系树与源文档一起编译为 HTML。 只有在您要编译 KiCad
文档时才需要 Doxygen。

## SWIG 简化的包装器和界面生成器

[SWIG](http://www.swig.org/) 是用于生成 KiCad 的 Python 脚本语言扩展。
如果您不打算编译 KiCad 脚本扩展，则不需要 SWIG。

# 库依赖项

本节包括编译 KiCad 所需的库依赖项列表。 它不包括库的任何依赖项。
有关任何其他依赖项，请参阅库文档。
其中一些库是可选的，具体取决于您的编译配置。
这不是关于如何使用系统包管理工具安装库依赖项或如何从源代码编译库的指南。
要执行这些任务，请参阅相应的文档。

## wxWidgets 跨平台 GUI 库

[wxWidgets](http://wxwidgets.org/) 是 KiCad 使用的图形用户界面 (GUI)
库。 当前的最低版本是 3.0.0。 但是，应该尽可能使用
3.0.2，因为以前版本中的一些已知错误可能会在某些平台上导致问题。
请注意，在从源代码编译 wxWidget 之前，还必须应用一些特定于平台的补丁。
这些补丁程序可以在 KiCad 源代码的 [patches
文件夹](https://gitlab.com/kicad/code/kicad/-/tree/master/patches)
中找到。 这些补丁由应该应用它们的 wxWidgets 版本和平台名称命名。wxWidget
必须使用 --with-opengl 选项编译。 如果您的系统上安装了 wxWidgets
的打包版本，请验证它是否使用此选项编译。

## Boost C++ 库

仅当您打算使用系统安装的 Boost 版本而不是默认的内部编译版本编译 KiCad
时，才需要 [Boost](https://www.boost.org/) C++ 库。 如果使用系统安装的
Boost 版本，则需要 1.56 或更高版本。请注意，编译有效的 Boost
库需要一些特定于平台的补丁。 这些补丁程序可以在 KiCad 源代码的 [patches
文件夹](https://gitlab.com/kicad/code/kicad/-/tree/master/patches)
中找到。 这些补丁程序按应应用它们的平台名称命名。

## GLEW OpenGL Extension Wrangler 库

[OpenGL Extension Wrangler](http://glew.sourceforge.net/) 是 KiCad
图形抽象库 \[GAL\] 使用的 OpenGLhelper 库， 在编译 KiCad 时总是需要的。

## ZLib 库

KiCad 使用 [ZLib](http://www.zlib.net/) 开发库来处理压缩的 3D 模型
(.stpz 和 .wrz 文件)， 并且在编译 KiCad 时始终需要该开发库。

## GLM OpenGL Mathematics 库

[OpenGL Mathematics 库](http://glm.g-truc.net/) 是由 KiCad 图形抽象库
\[GAL\] 使用的 OpenGLHelper 库， 并且总是编译 KiCad 所必需的。

## GLUT OpenGL 实用程序工具箱库

[OpenGL Utility
Toolkit](https://www.opengl.org/resources/libraries/glut/) 是 KiCad
图形抽象库 \[GAL\] 使用的 OpenGL 辅助程序库，在编译 KiCad 时始终需要它。

## Cairo 2D 图形库

[Cairo](http://cairographics.org/) 不可用时，OpenGL2D
图形库用作备用渲染画布，并且始终需要它来编译 KiCad。

## Python 编程语言

[Python](https://www.python.org/) 编程语言用于为 KiCad 提供脚本支持。
除非禁用 \[KiCad 脚本\](#kicad_script) 编译配置选项，否则需要安装它。

## wxPython 库

[wxPython](http://wxpython.org/) 库用于为 pcbnew 提供脚本控制台。
除非禁用 \[wxPython脚本\](#wxpython_script)
编译配置选项，否则需要安装它。 在支持 wxPython 的情况下编译 KiCad
时，请确保 wxWidgets 库的版本与系统上安装的 wxPython 版本相同。
已知不匹配的版本会导致运行时问题。

## Curl 多协议文件传输库

[Curl 多协议文件传输库](http://curl.haxx.se/libcurl/) 用于为
\[GitHub\]\[\] 插件提供安全的互联网文件传输访问。 除非禁用 GitHub Plug
编译选项，否则需要安装此库。

## OpenCascade 库

[Open CASCSADE 技术 (OCC)](https://www.opencascade.com/content/overview)
用于提供对加载和保存 3D 模型文件格式(如 STEP)的支持。 除非禁用 OCC
编译选项，否则需要安装此库。使用选项 BUILD_MODULE_DRAW=OFF 编译 OCC
时，使编译更加容易

### Ngspice 库

[Ngspice 库](https://sourceforge.net/projects/ngspice/)
用于在原理图编辑器中提供 SPICE 模拟支持。 确保使用的 ngspice
库版本是使用 --with-ngshare 选项编译的。除非禁用 Spice
生成选项，否则需要安装此库。

# KiCad 生成配置选项

KiCad
有许多编译选项，可以配置这些选项来编译不同的选项，具体取决于给定平台上对每个选项的支持情况。
本节记录这些选项及其默认值。

## 脚本支持

KICAD_SCRIPTING 选项用于将 Python 脚本支持编译到 Pcbnew 中。
默认情况下，此选项处于启用状态，并且在禁用时将禁用所有其他
KICAD_SCRIPTING\_\* 选项。

## Python 3 脚本支持

KICAD_SCRIPTING_PYTHON3 选项用于启用使用 Python 3 而不是 Python 2
进行脚本支持。 默认情况下，此选项处于禁用状态，仅当启用
\[KICAD_SCRIPTING\](#scripting_opt) 时才相关。

## 脚本模块支持

KICAD_SCRIPTING_MODULES 选项用于支持编译和安装 KiCad 提供的 Python
模块。 默认情况下，此选项处于启用状态，但如果禁用
\[KICAD_SCRIPTING\](#scripting_opt)，则此选项将被禁用。

## wxPython 脚本支持

KICAD_SCRIPTING_WXPYTHON 选项用于将 wxPython 接口编译到 Pcbnew 中，包括
wxPython 控制台。 默认情况下，此选项处于启用状态，但如果禁用
\[KICAD_SCRIPTING\](#scripting_opt)，则此选项将被禁用。

## wxPython Phoenix 脚本支持

KICAD_SCRIPTING_WXPYTHON_PHOENIX 选项用于使用新的 Phoenix
绑定(而不是旧的绑定)编译 wxPython 接口。
默认情况下该选项处于禁用状态，启用该选项需要启用
\[KICAD_SCRIPTING\](#scripting_opt)。

## Python 脚本操作菜单支持

KICAD_SCRIPTING_ACTION_MENU 选项允许将 Python 脚本直接添加到 Pcbnew
菜单。 默认情况下，此选项处于启用状态，但如果禁用
\[KICAD_SCRIPTING\](#scripting_opt)，则此选项将被禁用。
请注意，此选项是高度实验性的，如果 Python 脚本在 Pcbnew
中创建无效的对象状态，可能会导致 Pcbnew 崩溃。

## 集成 Spice 仿真器

KICAD_SPICE 选项用于控制是否为 EesChema 编译 Spice
仿真器接口。启用此选项时，它要求 \[ngspice\]\[\] 作为共享库可用。
默认情况下，此选项处于启用状态。

## 对 3D 查看器的 STEP/IGES 支持

KICAD_USE_OCC 用于 3D 查看器插件以支持 STEP 和 IGES 3D 模型。
此选项启用与 OpenCascade (OCC) 相关的编译工具和插件。 启用时，它要求
\[libocct\]\[\] 可用。 默认情况下，此选项处于启用状态。

## Wayland EGL 支持

KICAD_USE_EGL 选项将 OpenGL 后端从使用 X11 绑定切换到 Wayland EGL 绑定。
只有在运行 wxWidgets 3.1.5+ 和 wxGLCanvas 的 EGL 后端时，该选项才与
Linux 相关(这是默认选项，但可以在 wxWidgets 编译中禁用)。

默认情况下，设置 KICAD_USE_EGL 将使用静态链接到 KiCad 的 Glew
库的树内版本(使用在 EGL 画布上运行所需的附加标志进行编译)。 如果 Glew
的系统版本支持 EGL (必须使用 GLEW_EGL 标志进行编译)，则可以通过将
KICAD_USE_Bundled_Glew 设置为 OFF 来使用它。

## Windows HiDPI 支持

KICAD_WIN32_DPI_AWARE 选项使 KiCad 的 Windows 清单文件使用支持 DPI
的版本， 该版本告诉 Windows KiCad 希望每个监视器 V2 识别 DPI (需要
Windows 10 版本 1607 和更高版本)。

## 开发分析工具

KiCad 可以编译为支持多个功能，以帮助捕获和调试运行时内存问题

### Valgrind 支持

KICAD_USE_VALGRIND 选项用于在工具框架中启用 Valgrind 的堆栈注释功能。
这为 Valgrind
提供了跟踪工具框架中的内存分配和访问的能力，并减少了报告的误报数量。
默认情况下，此选项处于禁用状态。

### C++ 标准库调试

KiCad 提供了两个选项来启用 GCC C++
标准库中包含的调试断言：KICAD_STDLIB_DEBUG 和 KICAD_STDLIB_LIGHT_DEBUG。
默认情况下，这两个选项都处于禁用状态，并且一次只应打开一个选项，且
KICAD_STDLIB_DEBUG 优先。

KICAD_STDLIB_LIGHT_DEBUG 选项通过将 `_GLIBCXX_ASSERTIONS` 传递到
CXXFLAGS 来启用轻量级标准库断言。
这允许对字符串、数组和向量进行边界检查，以及对智能指针进行空指针检查。

KICAD_STDLIB_DEBUG 选项通过将 `_GLIBCXX_DEBUG` 传递到 CXXFLAGS
来启用全套标准库断言。 这启用了对标准库的完全调试支持。

### Address Sanitizer 支持

KICAD_SANITIZE
选项启用地址清理程序支持，以跟踪内存分配和访问以确定问题。
默认情况下，此选项处于禁用状态。Address Saniizer
包含多个运行时选项，用于调整其行为， 在其
[文档](https://github.com/google/sanitizers/wiki/AddressSanitizerFlags)
中有更详细的描述。

并非所有编译系统都支持此选项，并且已知在使用 mingw 时会出现问题。

## 演示和示例

KiCad 源代码包括一些演示和示例来展示该程序。您可以使用
KICAD_INSTALL_DEMOS 选项选择是否安装它们。 您还可以使用 KICAD_DEMOS
变量选择它们的安装位置。在 Linux 上，演示程序安装在。 默认情况下为
\$prefix/share/kicad/demos。

## 质量保证 (QA) 单元测试

KICAD_BUILD_QA_TESTS
选项允许编译用于质量保证的单元测试二进制文件，作为默认编译的一部分。
默认情况下，此选项处于启用状态。

如果禁用此选项，仍可以通过手动指定目标来编译 QA 二进制文件。 以 `make`
为例：

- 编译所有 QA 二进制文件: `make qa_all`

- 编译特定的测试: `make qa_pcbnew`

- 编译所有单元测试: `make qa_all_tests`

- 生成所有测试工具二进制文件: `make qa_all_tools`

有关测试 KiCad 的更多信息，请参阅 \[本页\](testing.md)。

## KiCad 编译版本

当 git 可用时，KiCad 版本字符串由 `git Describe—​dirty` 的输出定义，
或者由 CMakeModules/KiCadVersion.cmake 中定义的版本字符串定义，
并在前者后面附加 KICAD_VERSION_EXTRA 的值。如果未定义
KICAD_VERSION_EXTRA 变量， 则不会将其附加到版本字符串。如果定义了
KICAD_VERSION_EXTRA 变量， 则会将其与前导 '-'
一起追加到完整版本字符串，如下所示：

    (KICAD_VERSION[-KICAD_VERSION_EXTRA])

生成脚本自动从 \[git\]\[\] 仓库中创建版本字符串信息。 有关资料如下：

    (5.0.0-rc2-dev-100-g5a33f0960)
     |
     如果 git 可用，则输出 `git describe --dirty`。

## KiCad 配置目录

KiCad 默认配置目录为 `kicad`。在 Linux 上位于 `~/.config/kicad`， 在 MSW
上位于 `C：\Documents and Settings\用户名\Application Data\kicad`， 在
MacOS 上位于 `~/Library/Preferences/kicad`。
如果安装包愿意，它可以指定一个替代配置名称，而不是 `kicad`。
这对于对配置参数进行版本化并且允许同时使用例如 `kicad5` 和 `kicad6`
而不丢失配置数据可能是有用的。

这是通过在编译时指定 KICAD_CONFIG_DIR 字符串来设置的。

# 获取 KiCad 源代码

有几种方法可以获得 KiCad 源代码。如果您想编译稳定版本，可以从
\[GitLab\]\[\] 仓库下载源压缩包。 使用 tar
或其他压缩程序解压系统上的源代码。如果您使用的是 tar，请使用以下命令：

``` sh
tar -xaf kicad_src_archive.tar.xz
```

如果您直接参与 GitLab 上的 KiCad
项目，则可以使用以下命令在您的计算机上创建本地副本：

``` sh
git clone https://gitlab.com/kicad/code/kicad.git
```

以下是源链接列表：

稳定的版本压缩包： <https://kicad.org/download/source/>

开发分支： <https://gitlab.com/kicad/code/kicad/tree/master>

GitHub 镜像: <https://github.com/KiCad/kicad-source-mirror>

# 已知问题

有些已知问题会影响所有平台。本节提供了在任何平台上编译 KiCad
时当前已知问题的列表。

## Boost C++ 库问题

从 [GNU GCC 版本 5 开始](https://gcc.gnu.org/)，使用下载、修补和编译
Boost 1.54 的默认配置将导致 KiCad构 建失败。 因此，必须用更新版本的
Boost 来编译 KiCad。如果您系统安装了 Boost 1.56
或更高版本，则您的工作非常简单。 如果您的系统未安装 Boost 1.56
或更高版本，则必须从源代码下载和 [编译
Boost](http://www.boost.org/doc/libs/1_59_0/more/getting_started/index.html)。
如果您使用 [MinGW](http://mingw.org/) 在 Windows 上编译
Boost，则必须应用 KiCad 源 [patches
文件夹](https://gitlab.com/kicad/code/kicad/-/tree/master/patches)中的
Boost 补丁。
