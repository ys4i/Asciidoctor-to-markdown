title: 编译选项 weight: 17 summary: 通过 CMake 配置编译选项摘要 ---

这些是在配置过程中传递给 cmake 的选项

# 所有平台

<table>
<colgroup>
<col style="width: 23%" />
<col style="width: 70%" />
<col style="width: 5%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">选项</th>
<th style="text-align: left;">描述</th>
<th style="text-align: left;">默认</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_SCRIPTING</p></td>
<td style="text-align: left;"><p>在 KiCad 二进制文件中编译 Python
脚本支持。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_SCRIPTING_MODULES</p></td>
<td style="text-align: left;"><p>编译 pcbnew Python
模块的本机部分：_pcbnew.{pyd，so}，以便操作系统命令行使用 Python。<br />
如果使用 WXPYTHON，则 python 版本必须与用于编译 wxPytho
n的版本相同</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_SCRIPTING_PYTHON3</p></td>
<td style="text-align: left;"><p>为 Python 3 而不是 2 编译。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_SCRIPTING_WXPYTHON</p></td>
<td style="text-align: left;"><p>在 Python 和 py.shell 中编译用于编译 WX
接口的 wxPython 实现。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p>KICAD_SCRIPTING_WXPYTHON_PHOENIX</p></td>
<td style="text-align: left;"><p>使用新的 wxPython 绑定。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_SCRIPTING_ACTION_MENU</p></td>
<td style="text-align: left;"><p>使用注册的 python
插件编译工具菜单：操作插件。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_USE_OCC</p></td>
<td style="text-align: left;"><p>编译与 OpenCascade
技术相关的工具和插件。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_INSTALL_DEMOS</p></td>
<td style="text-align: left;"><p>安装 KiCad 演示和示例。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_BUILD_QA_TESTS</p></td>
<td style="text-align: left;"><p>编译软件质量保证单元测试。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_SPICE</p></td>
<td style="text-align: left;"><p>使用内部 Spice 仿真器编译
KiCad。</p></td>
<td style="text-align: left;"><p>ON</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_BUILD_I18N</p></td>
<td style="text-align: left;"><p>编译翻译语言库</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_I18N_UNIX_STRICT_PATH</p></td>
<td style="text-align: left;"><p>将语言库安装到标准 UNIX 安装路径<br />
${CMAKE_INSTALL_PREFIX}/share/locale</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>BUILD_SMALL_DEBUG_FILES</p></td>
<td
style="text-align: left;"><p>在调试版本中：创建较小的二进制文件。<br />
在 Windows 上，链接选项 -g3 创建的二进制文件
<strong>非常大</strong><br />
(pcbnew 超过 1 GB，完整 kicad 工具箱超过 3 GB)<br />
此选项使用链接选项 -g1 创建二进制文件，+ 但是 debug 中的信息较少
(但是提供了文件名和行号)<br />
</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>MAINTAIN_PNGS</p></td>
<td style="text-align: left;"><p>允许从相应的 .svg
文件生成/重建菜单中使用的位图图标。<br />
如果您是PNG维护者，并且在 bitmap_png/CMakeLists.txt 文件<br />
中提供了所需的工具，则设置为 true</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
</tbody>
</table>

# 并非所有平台都支持

<table>
<colgroup>
<col style="width: 23%" />
<col style="width: 70%" />
<col style="width: 5%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">选项</th>
<th style="text-align: left;">描述</th>
<th style="text-align: left;">默认</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_SANITIZE</p></td>
<td style="text-align: left;"><p>使用 sanitizer 选项编译KiCad。<br />
警告：与 gold 链接器不兼容。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_STDLIB_DEBUG</p></td>
<td style="text-align: left;"><p>在启用 libstdc++ 调试标志的情况下编译
KiCad。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_STDLIB_LIGHT_DEBUG</p></td>
<td style="text-align: left;"><p>使用 libstdc++ 编译 KiCad，并启用
-Wp,-D_GLIBCXX_ASSERTIONS 标志。<br />
不像 KICAD_STDLIB_DEBUG 那样具有侵入性</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>KICAD_BUILD_PARALLEL_CL_MP</p></td>
<td style="text-align: left;"><p>使用 /MP
编译器选项并行编译(出于安全原因，默认设置为关闭)。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>KICAD_USE_VALGRIND</p></td>
<td style="text-align: left;"><p>在启用 valgrind 堆栈跟踪的情况下生成
KiCad。</p></td>
<td style="text-align: left;"><p>OFF</p></td>
</tr>
</tbody>
</table>

# 注意

启用选项 `KICAD_SCRIPTING` 或 `KICAD_SCRIPTING` 或
`KICAD_SCRIPTING_MODULES` 时：

调用 cmake 时可以定义 `PYTHON_EXECUTABLE` ( 使用
`-DPYTHON_EXECUTABLE=<python 路径>/python.exe` 或 python2 )
用户未定义时，Windows 下默认为 python.exe，其他系统默认为 python2。
python 二进制文件应在 exec 路径中。

## 注意 1

KICAD_SCRIPTING 控制整个 Python 脚本系统。
如果该选项处于关闭状态，则不允许编写其他脚本

因此，如果 `KICAD_SCRIPTING` 为 OFF，则这些其他选项将被强制关闭：
`KICAD_SCRIPTING_MODULES, KICAD_SCRIPTING_ACTION_MENU,KICAD_SCRIPTING_PYTHON3 KICAD_SCRIPTING_WXPYTHON, KICAD_SCRIPTING_WXPYTHON_PHOENIX`

## 注意 2

`KICAD_SCRIPTING_WXPYTHON_PHOENIX` 需要启用 `KICAD_SCRIPTING_WXPYTHON`
标志。 因此 wxWidgets 库的版本设置正确

## 注意 3

这些符号始终是已定义的，不是 cmake 调用的选项：

**COMPILING_DLL**

这是一个指向 import_export.h 的信号，当它出现时， 会切换对该文件中的
\#defines 的解释。 它的目的不应该超出这一点。

**USE_KIWAY_DLLS**

作为用户配置变量来自 CMake，可在 CMake 用户界面中设置。 它决定 KiCad
是否使用 \*.kiface 程序模块编译。

**BUILD_KIWAY_DLL**

来自 CMake，但在第二层，而不是顶级。第二层指的是
pcbnew/CMakeLists.txt，而不是 /CMakeLists.txt。
它不是用户配置变量。相反，第二层 CMakeLists.txt 文件查看顶级
`USE_KIWAY_DLLS`， 并决定如何编译第二层控制下的目标文件。如果它决定与
`USE_KIWAY_DLLS` 步调一致， 则此本地 CMakeLists.txt
文件可能会将编译器命令行上定义的 `BUILD_KIWAY_DLL` (单数)
传递给其控制下的相关编译步骤集。

## 注意 4

在 Linux 系统上使用 `KICAD_BUILD_I18N` 编译时，gettext 需要规则文件
`shared-mime-info.its` 和 `metainfo.its` / `appdata.its` 来翻译 Linux
元数据文件。
