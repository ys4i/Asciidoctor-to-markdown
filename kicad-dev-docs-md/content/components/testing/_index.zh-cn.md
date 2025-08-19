title: 测试框架 weight: 20 ---

# 单元测试

KiCad 的单元测试数量有限， 可用于检查某些功能是否正常工作。

测试是使用 CMake 的
[CTest](https://cmake.org/cmake/help/latest/module/CTest.html)，组件注册的。
CTest 收集并运行所有不同的测试程序。 大多数 C++ 单元测试都是使用 [Boost
单元测试框架编写的](https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/index.html)，
但这不是向测试套件添加测试所必需的。

测试 CMake 目标一般以 `qa_` 开头，CTest 内的测试名称相同， 但没有 `qa_`
前缀。

## 运行测试

您可以使用 `make test` 或 `ctest` 进行构建后的所有测试。
后一选项允许许多 CTest 选项，这些选项可能非常有用，尤其是在自动化或 CI
环境中。

### 运行特定测试

如果要运行特定的测试可执行文件，可以直接使用 `ctest`
运行，也可以直接运行该可执行文件。
在处理特定测试并且您希望访问测试可执行文件的参数时，直接运行通常是最简单的方式。
例如：

``` sh
# 运行 libcommon 测试
cd /path/to/kicad/build
qa/tests/common/qa_common [parameters]
```

对于 Boost 单元测试，可以使用 `<test>--help` 查看测试选项。
常见的有用模式：

- `<test> -t "Utf8/*"` 运行 `Utf8` 测试套件中的所有测试。

- `<test> -t "Utf8/UniIterNull"` 仅在特定套件中运行单个测试。

- `<test> -l all` 向输出添加更详细的调试。

- `<test> --list_content` 中列出测试套件和测试用例。
  测试程序。您可以使用这些参数作为 `-t` 的参数。

您可以使用 CMake 只重建特定的测试，避免在小范围工作时重建所有测试， 例如
`make qa_common`。

### 自动化测试

单元测试可以在自动化持续集成 (CI) 系统上运行。

默认情况下，测试输出人类可读的结果，这在开发或调试时很有用，但对于自动测试报告则不太有用。
可以解析 XML 测试结果的系统可以通过将 `KICAD_TEST_XML_OUTPUT` 选项设置为
`ON` 来启用这些功能。 然后将测试结果输出为 `qa` 子目录中以 `.xml`
结尾的文件。

测试结果按如下方式写入 Build 目录：

- Boost 单元测试：每个测试都有一个扩展名为的 XML 文件
  `.boost-results.xml`

- Python 单元测试：每个扩展名为的测试一个目录 `.xunit-results.xml`.
  这些目录中的每个 Python 测试用例文件都包含一个 `.xml` 文件。

## 编写 Boost 测试

Boost 单元测试编写起来很简单。 单独的测试用例可以注册到：

``` cpp
BOOST_AUTO_TEST_CASE( SomeTest )
{
    BOOST_CHECK_EQUAL( 1, 1 );
}
```

有一系列像
`` BOOST_CHECK`这样的函数，它们记录在 这里。 最好使用最具体的函数，因为这样 Boost 可以提供更详细的故障： `BOOST_CHECK( foo == bar ) ``
只报告不匹配， `BOOST_CHECK_EQUAL( foo, bar )` 将显示每个错误的值。

若要输出调试消息，您可以在单元测试中使用 `BOOST_TEST_MESSAGE`， 只有在
`-l` 参数设置为 `message` 或更高的时候才能看到，
这样文本的颜色会有所不同，以区别于其他测试消息和标准输出。

您也可以使用 `std::cout`、`printf`、`wxLogDebug`
等来处理测试函数内部的调试消息 (即您没有权限访问 Boost
单元测试头文件的地方)。这些文件总是打印出来的，
所以在提交之前一定要删除它们，否则当 KiCad 正常运行时它们就会出现！

### 预期失败

有时，检查未通过的测试很有帮助。 但是，故意检查提交中断编译 (如果导致
`make test` 失败就会发生这种情况) 是不好的做法。

Boost 提供了一种声明允许某些特定测试失败的方法。 此语法并非在所有支持的
Boost 版本中都一致可用， 因此应使用以下结构：

``` cpp
#include <unit_test_utils/unit_test_utils.h>

// 在使用较旧 boosts 的平台上，该测试将被完全排除
#ifdef HAVE_EXPECTED_FAILURES

// 声明一个测试用例有 1 个 “允许的” 失败 (在本例中为 2 个)
BOOST_AUTO_TEST_CASE( SomeTest, *boost::unit_test::expected_failures( 1 ) )
{
    BOOST_CHECK_EQUAL( 1, 1 );

    // 此检查失败，但不会导致测试套件失败
    BOOST_CHECK_EQUAL( 1, 2 );

    // 进一步的失败 *会不会* 是测试套装失败
}

#endif
```

运行时，这会产生类似以下内容的输出：

``` sh
qa/common/test_mytest.cpp(123): error: in "MyTests/SomeTest": check 1 == 2 has failed [1 != 2
*** No errors detected
```

并且单元测试可执行文件返回 `0` (成功)。

检查失败的测试是一种严格意义上的临时性情况，
用于说明在修复错误之前是否触发了该错误。 这不仅从 “项目历史”
的角度来看是有利的， 而且还可以确保您为捕获所讨论的 bug
而编写的测试实际上首先捕获了 bug。

### 断言

可以在单元测试中检查断言。 在运行单元测试时，会捕获 `wxASSERT`
调用并将其作为异常重新抛出。 然后，您可以使用 `CHECK_WX_ASSERT`
宏来检查这在调试版本中是否被调用。
在发布版本中，不会运行检查，因为在这些版本中禁用了 `wxASSERT`。

您可以使用它来确保代码正确地拒绝无效输入。

## Python 模块

Pcbnew Python 模块在 `qa` 目录下有一些测试程序， 您必须在 CMake 中打开
`KICAD_SCRIPTING_MODULES` 选项， 才能编译模块并启用该目标。

主测试脚本为 `qa/test.py`，测试单元在 `qa/testcases` 中。
所有测试单元都可以通过运行 `test.py` 的 `ctest python` 来运行。

您也可以手动运行单个案例，方法是确保编译模块， 将其添加到
`PYTHONPATH`，然后从源码工作区运行测试：

``` sh
make pcbnew_python_module
export PYTHONPATH=/path/to/kicad/build/pcbnew
cd /path/to/kicad/source/qa
python2 testcase/test_001_pcb_load.py
```

### 诊断分段故障

虽然该模块是 Python，但它链接到 C++ 库 (与 KiCad Pcbnew 使用的库相同)，
因此如果库有缺陷，它可以分段错误。

您可以在 GDB 中运行测试来跟踪以下内容：

``` sh
$ gdb

(gdb) file python2
(gdb) run testcases/test_001_pcb_load.py
```

如果测试分段失败，您将得到熟悉的断点， 就像在 GDB 下运行 pcbnew 一样。

# 实用程序

KiCad 包括一些实用程序，可用于调试、剖析、分析或开发代码的某些部分，
而不必调用完整的 GUI 程序。

通常，它们是 `qa_*_tools` QA
可执行文件的一部分，每个可执行文件都包含该库的相关工具。
要列出给定程序中的工具，请传递 `-l` 参数。大多数工具都提供了对 `-h`
参数的帮助。 要调用程序，请执行以下操作：

    qa_<lib>_tools <tool name> [-h] [tool arguments]

下面是一些可用工具的简要概述。 完整信息和命令行参数请参考工具使用测试
(`-h`)。

- `common_tools` (通用库和核心函数)：

- `coroutine`: 一个简单的协同例程示例

- `io_benchmark`: 显示使用各种 IO 技术读取文件的相对速度。

- `qa_pcbnew_tools` (pcbnew 相关函数)：

- `drc`: 在用户提供的 `.kicad_pcb`
  文件上运行某些DRC函数并对其进行基准测试

- `pcb_parser`: 解析用户提供的 `.kicad_pcb` 文件

- `polygon_generator`: 将 PCB 上发现的多边形转储到控制台

- `polygon_triangulation`: 对 PCB 上的区域多边形执行三角剖分

# 模糊测试

可以在 KiCad 的某些部分运行模糊测试。
要对泛型函数执行此操作，您需要能够将来自模糊测试工具的某种输入传递给被测函数。

例如，要使用 [AFL 模糊工具](http://lcamtuf.coredump.cx/afl/)，您需要：

- 一个测试可执行文件，它可以：

- 接收 `stdin` 的输入，由 `afl-fuzz` 运行。

- 可选：处理来自文件名的输入，以允许 `afl-tmin` 最小化输入文件。

- 使用 AFL
  编译器编译此可执行文件，以启用允许模糊器检测模糊状态的指令插入。

例如，`qa_pcbnew_tools` 可执行文件 (包含用于 `.kicad_pcb`
文件解析的模糊测试工具 `pcb_parser`) 可以这样编译：

``` sh
mkdir build
cd build
cmake -DCMAKE_CXX_COMPILER=/usr/bin/afl-clang-fast++ -DCMAKE_C_COMPILER=/usr/bin/afl-clang-fast ../kicad_src
make qa_pcbnew_tools
```

您可能需要在系统上禁用核心转储和 CPU 频率调节 (AFL
会警告您是否应该这样做)。 例如，以 root 用户身份：

``` sh
    # echo core >/proc/sys/kernel/core_pattern
    # echo performance | tee cpu*/cpufreq/scaling_governor
```

要进行模糊处理，可以通过 `afl-fuzz` 运行可执行文件：

    afl-fuzz -i fuzzin -o fuzzout -m500 qa/pcbnew_tools/qa_pcbnew_tools pcb_parser

在哪里：

- `-i` 是用作模糊输入 "seeds" 的文件目录。

- `-o` 是写入结果的目录 (包括引起崩溃或挂起的输入)

- `-t` 是在被宣布为 “挂起” 之前允许运行的最长时间。

- `-m` 是否允许使用内存 (这通常需要调整，因为 KiCad
  代码往往会使用大量内存进行初始化)

然后，AFL TUI
将显示模糊进度，您可以根据需要使用挂起或引发崩溃的输入来调试代码。

# 运行时调试

KiCad 可以在运行时调试，既可以在完整的调试器 (如 GDB) 下调试，
也可以使用简单的方法 (如将调试记录到控制台) 进行调试。

## 打印调试

如果您自己编译 KiCad，只需在代码中的相关位置添加调试语句即可，例如：

    wxLogDebug( "Value of variable: %d", my_int );

这会生成调试输出，只有在调试模式下编译时才能看到。

您也可以使用 `std::cout` 和 `printf`。

请确保在提交代码时不要将这种调试留在原地。

## 打印跟踪

代码的某些部分具有可以根据 "掩码 有选择地启用的 "跟踪"，例如：

    wxLogTrace( "TRACEMASK", "My trace, value: %d", my_int );

默认情况下不会打印此文件。 要显示它，请在运行 KiCad 时设置 `WXTRACE`
环境变量，以包含要启用的掩码：

    $ WXTRACE="TRACEMASK,OTHERMASK" kicad

打印时，调试将以时间戳和跟踪掩码为前缀：

    11:22:33: Trace: (TRACEMASK) My trace, value: 42

如果添加跟踪掩码，请将该掩码定义并记录为 `include/trace_helpers.h`
中的变量。 这会将其添加到
[跟踪掩码文档](http://docs.kicad.org/doxygen/group__trace__env__vars.html)中。

一些可用的掩码：

- KiCad 核心功能：

- `KICAD_KEY_EVENTS`

- `KicadScrollSettings`

- `KICAD_FIND_ITEM`

- `KICAD_FIND_REPLACE`

- `KICAD_NGSPICE`

- `KICAD_PLUGINLOADER`

- `GAL_PROFILE`

- `GAL_CACHED_CONTAINER`

- `PNS`

- `CN`

- `SCROLL_ZOOM` - 用于 GAL 中的滚轮缩放逻辑

- 插件特定 (包括 “标准” KiCad 格式)：

- `3D_CACHE`

- `3D_SG`

- `3D_RESOLVER`

- `3D_PLUGIN_MANAGER`

- `KI_TRACE_CCAMERA`

- `PLUGIN_IDF`

- `PLUGIN_VRML`

- `KICAD_SCH_LEGACY_PLUGIN`

- `KICAD_GEDA_PLUGIN`

- `KICAD_PCB_PLUGIN`

# 高级配置

有一些高级配置选项，主要用于开发或测试目的。

要设置这些选项，您可以创建文件 `kicad_Advanced` 并根据需要设置密钥
(当前列表的
[高级配置文档](http://docs.kicad.org/doxygen/namespaceAC__KEYS.html))。
您应该永远不需要将这些键设置为正常使用 -
如果您这样做了，那就是一个错误。

通过高级配置系统启用的任何功能都被认为是试验性的，因此不适合生产使用。
这些功能显然没有得到支持或被认为是经过充分测试的。
对于发现的缺陷，问题仍然是受欢迎的。
