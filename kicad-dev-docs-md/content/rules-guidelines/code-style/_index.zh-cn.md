title: 代码编程规范 weight: 2 summary: 任何贡献到 KiCad
代码仓库的代码都必须遵守的代码编程规范。 ---

# 1. 简介

本文档旨在为 KiCad 开发人员提供有关如何在 KiCad
中设置源代码样式和格式的参考指南。
它不是一个全面的编程指南，因为它没有讨论很多事情，如软件工程策略、源目录、现有类或如何国际化文本。
我们的目标是使所有的 KiCad 源代码都符合本指南。

# 1.1 为什么代码规范很重要

您可能会想，使用本文档中定义的样式并不能使您成为一名优秀的程序员，这样您就是正确的。
任何给定的编码样式都不能取代经验。
然而，任何有经验的程序员都会知道，比查看不是您喜欢的编码样式的代码更糟糕的是，
查看不是您首选的编码样式的二十种不同的编码样式。 一致性使 a)
问题更容易发现，b) 长时间查看代码更容易容忍。

# 1.2 强制执行

如果你不遵守 KiCad
代码规范，我们并不会闯入你家并用你的键盘暴揍你（虽然有一部分人认为应该这么做）。
但是，确实存在一些你应该遵守本文档的充分的理由。
假设你正在贡献一个补丁，如果你的补丁是按照规范格式的，那么它将会被主要开发者
(primary developers) 更加认真地对待。
忙碌的开发者没有时间帮你重新格式化你的代码。
如果你有意成为一个对开发分支有提交权限的正式 KiCad 开发者，
那么你就不应该经常因为不遵守代码编程规范而受到首席开发者 (lead
developers) 的批评。
遵守代码编程规范是一个好的编程礼仪，它体现出对已有的开发者作出的贡献的尊重。
其他 KiCad 开发者也会感谢你对此作出的努力。

**警告**

**未经项目负责人同意，请勿修改本文档。 对此文档的所有更改都需要审批。**

# 1.3 工具

这里有一些能够帮助你快速格式化代码的工具。

[clang-format](https://clang.llvm.org/docs/ClangFormat.html)
是个不仅能帮你 在你自己的编辑器里自动代码编程格式化的工具，它还能在你
git 提交代码时检查代码格式（使用 "Git Hook"）。 必须安装这个程序才能使用
"Git Hooks" 。

代码规范配置文件是 `_clang-format` ， 当设置了 `--style=file`
这个参数时， 这个文件应该会自动被 `clang-format` 自动读取。

你可以这样打开 Git 钩子(hooks)（这个操作每个 git 仓库只需要做一次）：

``` bash
git config core.hooksPath .githooks
```

设置 `git clang-format` 工具使用我们提供的 `_clang-format` 文件:

git config clangFormat.style file

然后通过设置 `kicad.check-format` git 配置项为 "true" 来为 KiCad
仓库打开代码格式检查：

``` bash
git config kicad.check-format true
```

如果没有配置这一配置项， 代码格式检查就不会在提交（commit）的时候运行，
但是你仍然可以手工检查在暂存去的文件（参见后文）。

如果钩子 （hook）是打开的，
当你提交改动时，将提示你所改动的代码是否违反了代码规范。
你可以相应地通过手工，或者使用下面的工具，来消除错误。

如果你确定你的代码格式是正确的，但还是出现格式错误警告，你可以这样来强制提交：

``` bash
git commit --no-verify
```

## 1.3.1 纠正格式错误

有一个 git 别名(alias) 文件提供了显示和纠正格式错误的命令。
通过下面的命令可以把它加入到你的仓库配置中：

``` bash
git config --add include.path $(pwd)/helpers/git/format_alias
```

之后，你就可以使用下面的别名：

- `git check-format`: 显示存在的格式警告 （但不做更改）

- `git fix-format`: 纠正格式 （之后你需要做一次 `git add`）

这些别名使用脚本
`tools/check-coding.sh`，这个脚本确保只检查那些应该遵循规范的文件。
这个脚本还有一个作用：

- 修正 （或者查看） 上一次提交修改的文件中的格式错误。这在交互式变基
  (interactive-rebasing) 中有用：

- `check_coding.sh --amend [--diff]`

# 2. 命名规范

在探讨神秘的缩进和格式问题之前，先要强调命名规范。
这一节，我们将定义命名的风格，
而不会定义代码应该用什么样的名字。请在参考文件一节获取一些优秀的编码指引。
当定义多单词名字时，使用下面的约定来改善可读性：

- 对于全部大写或者全部小写的变量名，使用下划线分隔单词使之易于识读。

- 对于大小写混合的变量名，使用驼峰命名法 (camel case) 。

不要混合驼峰命名法和下划线。

**例如**

        CamelCaseName           // 如果使用驼峰式就不要用下划线
        all_lower_case_name
        ALL_UPPER_CASE_NAME

## 2.1 类， 类型定义， 命名空间和宏名

类 (class), 类型定义 (typedef), 枚举 (enum), 命名空间 (name space) 和宏
(macro) 应该由大写字母组成。

**例如**

``` cpp
    class SIMPLE
    #define LONG_MACRO_WITH_UNDERSCORES
    typedef boost::ptr_vector<PIN>      PIN_LIST;
    enum KICAD_T {...};
```

## 2.2 局部变量， 私有变量和自动变量

自动变量，静态局部变量和私有变量的变量名第一个字符必须用小写字母。
以此来表示这些变量对它所被定义的函数，文件或者类的外部并不可见。
局部可见性由于开始的小写字母来表示，因为小写字母通常被认为是没有大写字母那么高调。

**例如**

``` cpp
    int i;
    double aPrivateVariable;
    static char* static_variable = NULL;
```

类和结构体的私有成员必须以 `m_` 开头，并且 `m_`
后面的第一个字母也必须是小写。

``` cpp
private:
    int m_privateData;
```

## 2.3 公开变量和全局变量

公开变量和全局变量的变量名第一个字母必须是大写字母。
以此来表示这个变量对它所被定义的类或者文件的外部可见。
（一个特例是，有时也会用以 `g_` 开头来表示全局变量）

**例如**

``` cpp
    char* GlobalVariable;
```

通常情况下， 类，不应该包含公开成员变量，而应该使用 getter/setter
方法来访问私有变量。
一个特例是，仅仅用来表示数据结构，和只包含少量的帮手方法的类。 比如，
`SEG` 类经常被当作数据结构来表示两点之间的直线段。 这个类有公开成员变量
`A` 和 `B` 用于保存它的端点。 这是可以接受的，因为 `SEG`
类符合只是用来表示数据结构的定义， 因为修改 `A` 或 `B`
的值时不需要其他连带动作。

## 2.4 局部函数，私有函数和静态函数

局部，私有和静态函数的第一个字母必须是小写字母。
以此来表示这个函数对它所被定义的类或者文件的外部不可见。

**例如**

``` cpp
    bool isModified();
    static int buildList( int* list );
```

## 2.5 函数参数

函数参数名称用 'a' 开头。 'a' 代表参数 (argument),
并且能够帮助生成智能和简约的 Doxygen 注释。

**例如**

``` cpp
    /**
     * 将 aFoo 复制到此实例中。
     */
    void SetFoo( int aFoo );
```

我们注意到，读者在读到这里的时候，可以默读成 “a Foo”。

## 2.6 指针

指针变量名并不需要内嵌 'p' 字母来表示。
指针代表一个变量从属于一个类型，而不是目标。

**例如**

``` cpp
    MODULE*   module;
```

这个变量的意义是代表一个 MODULE， `p_module`
这样的写法只会增加辨识的难度。

## 2.7 访问成员变量和成员函数

我们不在类的内部使用 `this→` 来访问成员变量或者成员函数。 我们让 C++
帮我们做到这一点。

## 2.8 'auto' 的使用

我们不使用 `auto` 来减少重复。 但我们可以用它来增加可读性。
也就是说仅仅在 std::lib 变得过度啰嗦，或者，不使用 `auto`
就会导致不可避免的折行情况下使用 `auto`。

# 3. 注释

在 KiCad 中，注释被分为两类：内联代码注释和 Doxygen 注释。
不跟随在语句之后的内联注释必须有和代码一致的缩进，除此之外没有其他格式要求。
跟随在语句之后的内联注释除非绝对必要，否则不应该超过 99 列。
避免在可视列宽为 100 的编辑器上发生自动换行。 内联注释可以既可以使用 C
也可以使用 C 的注释风格， 但是如果是单行或少量几行的注释，建议使用 C
风格。

避免出现显而易见的描述注释。 添加一个注释来说明这是一个 dtor, ctor,
function, iterator 之类的，
只是在注释中添加了无用的废话。只要有点经验的开发者就能看出来。
避免添加注释来描述代码做了什么。 代码做了什么应该很明显能被看出来，
不然，就说明代码需要被重写到能明显能看出来。

## 3.1 注释上面的空行

如果注释是一行的开始，那么这个注释上面应该有一行或多行空行。建议一行。

## 3.2 Doxygen

Doxygen 是本项目使用的一个 C++ 源代码文档工具. 安装 Doxygen 之后编译名叫
**doxygen-docs** 的目标， 就会从源代码生成描述性的 \*.html 文件。

        cd <kicad_build_base>
        make doxygen-docs

生成的 \*.html 文件将会放到下面的目录中
\<kicad_project_base\>/Documentation/doxygen/html/.

Doxygen 注释被用于从源代码编译出开发者文档。 这些注释通常只会放在头文件
(.h) 里而不放在代码文件 (.cpp) 里。 这样就避免需要同步两处注释的麻烦。
如果类，函数或者枚举等仅仅定义在一个代码文件中而不出现在头文件里，
这种情况下 Doxygen 注释就应该出现在代码文件里。 再说一遍， Doxygen
注释应该避免同时出现在头文件和代码文件中。

KiCad 使用 JAVADOC 注释风格，定义在 Doxygen
[手册](http://www.doxygen.nl/manual) 的
[doccode](http://www.doxygen.nl/manual/docblocks.html) 一节。 别忘了使用
[特殊 Doxygen 标签](https://www.doxygen.nl/manual/commands.html): bug,
todo, deprecated, 等。 这样其他开发者就能快速地找到你代码里的有用信息。
一个很好的习惯是，在提交你的补丁前，应当编译 doxygen-docs 目标生成
Doxygen \*.html 文件， 并在浏览器里检查你的 Doxygen 注释的质量。

不要定义类和函数名称，参数和返回数据类型。这些多余的信息其他开发者可以轻易地看出来，
并在生成的文档中重复出现。

尽量使用 [Doxygen’s
markdown](https://www.doxygen.nl/manual/markdown.html) 语法而不是 HTML
标签。 Markdown 注释比 HTML 更加易读。

当在 Doxygen 注释中引用其他代码的时候，使用 [Doxygen
链接](https://www.doxygen.nl/manual/autolink.html)。
这可以让其他开发者更容易找到信息。

使用 [Doxygen 分组命令](https://www.doxygen.nl/manual/grouping.html)
将相关的注释部分组合成一个组。
这样这些信息就不会散落在不同的文档页中，查找相关的信息会变得更方便。

### 3.2.1 函数注释

函数注释应该在头文件里，除非这个函数是这个源码文件私有的（即静态函数）。
格式化的函数注释有两个目的：在源代码文件里描述函数的声明和使 Doxygen
的文档输出有一致的开始句子。 格式要求是，
单一行描述性的句子，接一个空行，再接一个可选的详细描述。
下面是这个格式的例子。

**例如**

``` cpp
    /**
     *
     *
     * 格式化文本并将文本写入输出流。
     *
     * @param aMestLevel 是输出前面的空格倍数。
     * @param aFmt 是 printf() 样式的格式字符串。
     * @param ... 是将在格式字符串控制下混合到
     *  输出中的参数的变量列表。
     * @return 输出的字符数。
     * @throw IO_ERROR, 如果输出有问题。
     */
    int PRINTF_FUNC Print( int aNestLevel, const char* aFmt, ... );
```

单行的描述处于注释的第二行。 如果 @return 关键字出现的话，
返回值应该说明在一个连字符 (-) 之后。 @param 关键字之后跟函数的参数，
再之后的的文字应该和前面的名字组成一个正确的英语语句，包括标点。

### 3.2.2 类的注释

类的注释描述类的目的和用法来完成一个类的声明。 它的格式类似于函数格式。
Doxygen 可以使用 html \\p\\ (段落标记) 让输出中新起一个段落。
所以，如果注释的文本太长的话，可以根据需要把它们分成多段。

**例如**

``` cpp
    /**
     * An interface (abstract) class used to output UTF8 text in a
     * convenient way.
     *
     * The primary interface is "printf() like" but with support for
     * indentation control. The destination of the 8 bit wide text is
     * up to the implementer.
     * <p>
     * The implementer only has to implement the write() function, but
     * can also optionally re-implement GetQuoteChar().
     * <p>
     * If you want to output a wxString, then use CONV_TO_UTF8() on it
     * before passing it as an argument to Print().
     * <p>
     * Since this is an abstract interface, only classes derived from
     * this one may actually be used.
     */
    class OUTPUTFORMATTER
    {
```

# 4. 格式化

这一节定义了 KiCad 源代码的格式风格。

## 4.1 缩进

KiCad 源代码的一级缩进使用 4 个空格，请不要使用制表符 (tab)。

### 4.1.1 define 指令

`#define` 语句之后只应该有一个空格.

### 4.1.2 列对齐

请尽可能将个相似的行进行列对齐，例如，#define
语句，可以看成是由四列组成的， `#define`, 符号,
值和注释。请看下面的例子中，这四列是如何对齐的。

**例如** <sub>~~~~</sub>~{.cpp} \#define LN_RED 12 // my favorite
\#define LN_GREEN 13 // eco friendly <sub>~~~~</sub>~

还有一个常见的案例是自动变量的申明。尽量将他们按照类型和变量名进行列对齐。

## 4.2 空行

### 4.2.1 函数申明

类文件中的函数声明，如果有 Javadoc
注释，那么函数申明之前应该有一个空行。

### 4.2.2 函数定义

\*.cpp 文件中的函数定义通常不需要有注释，因为注释都在头文件里。 在
\*.cpp 文件中的函数定义，在函数定义前最好先空 2 个空行。

### 4.2.3 控制语句

控制语句的开语句前，关闭语句或者右花括号后都应该有一个空行，
这样就能很容易地识别出控制块的开头和结尾。 这里所说的控制块包括 `if`,
`for`, `while`, `do` 和 `switch`。

## 4.3 行宽

最大的列宽是 99
列。特例是引文的长字符串，比如一些国际化文本需要满足下面描述的 MSVC++
的要求。

## 4.4 字符串

KiCad 项目组不再支持用微软 Visual C++ 的编译。
当你需要将长字符串分割成多个段字符串时，请使用 C99
标准的方法来改善可读性。
下面描述的以前接受的分割国际化文本的方法现在已经不再接受了。

**例如**

``` cpp
    // This works with C99 compliant compilers is the **only** accepted method:
    // 这种方式可以运行在符合 C99 标准的编译器上， 这是唯一接受的方法：
    wxChar* foo = _( “this is a long string broken ”
                     “into pieces for readability.” );

    // This works with MSVC, breaks POEdit, and is **not** acceptable:
    // 这种方式可以运行在 MSVC 上, POEdit 不支持, 而且现在已经不再接受了：
    wxChar* foo = _( “this is a long string broken ”
                    L“into pieces for readability” );

    // This works with MSVC, is ugly, and is **not** accepted:
    // 这种方式可以运行在 MSVC 上, 很丑, 而且现在已经不再接受了：
    wxChar* foo = _( “this is a long string \
    broken into pieces for readability” );
```

另外一种可接受的方案是将字符文本放在一行，即使它超过了 99 列的行宽限制。
但是， 更建议尽量将字符串分割不超过 99 列以防止自动换行。

## 4.5 行尾空白字符

很多编程编辑器可以很方便的帮你缩进代码。但是其中一些在处理得不太好，
会在行尾留下空白字符。幸运的是，大多数编辑器都包含删除尾部空白字符的宏，
或者至少有一个设置使得尾部的空行可以被看见以便可以手工删除它。
尾部空白字符会导致一些文本分析工具失效，还会导致版本管理系统中产生一些不必要的差异
(diffs)。 所以，请删除行尾空白字符。

## 4.6 一行多语句

我们建议一条语句占一行。对于没有关键字的语句尤其要独占一行。

``` cpp
    x=1; y=2; z=3; // 坏的，应该在独占一行。
```

## 4.7 大括号

大括号应该在关键字后面一行，而且和关键字缩进等级一致。
如果只有一条语句， 就不需要使用大括号。在 if..else if..else 这种语句中，
将他们缩进到同一个等级。

``` cpp
    void function()
    {
        if( foo )
        {
            statement1;
            statement2;
        }
        else if( bar )
        {
            statement3;
            statement4;
        }
        else
            statement5;
    }
```

## 4.8 括号

括号应该立即跟在函数名和关键字后面。函数中开括号后面，闭括号前，
逗号和下一个参数之间应该留有一个空格。如果函数没有参数，那就不需要留空格。

``` cpp
    void Function( int aArg1, int aArg2 )
    {
        while( busy )
        {
            if( a || b || c )
                doSomething();
            else
                doSomethingElse();
        }
    }
```

## 4.9 Switch 格式

case 语句应该和 switch 在同一个缩进级。

``` cpp
    switch( foo )
    {
    case 1:
        doOne();
        break;
    case 2:
        doTwo();
        // 失败了。
    default:
        doDefault();
    }
```

如果能提高可读性，最好将所有的 case
放在一行里。在查值或者映射函数中经常用这种方式。
在这种情况下，如果你使用 clang-format
，就需要拒绝它的的建议，使用手动对齐来增加可读性：

``` cpp
    switch( m_orientation )
    {
    case PIN_RIGHT: m_orientation = PIN_UP;    break;
    case PIN_UP:    m_orientation = PIN_LEFT;  break;
    case PIN_LEFT:  m_orientation = PIN_DOWN;  break;
    case PIN_DOWN:  m_orientation = PIN_RIGHT; break;
    }
```

## 4.10 Lamdas

大括号和语句体应该像缩进一个方法那样缩进，大括号与上面的语句对齐。

``` cpp
    auto belowCondition = []( const SELECTION& aSel )
                          {
                              return g_CurrentSheet->Last() != g_RootSheet;
                          };
```

or:

``` cpp
    auto belowCondition =
        []( const SELECTION& aSel )
        {
            return g_CurrentSheet->Last() != g_RootSheet;
        };
```

## 4.11 类定义布局

成员变量应该在类定义的最底端。类成员的可见性排序应该按照公开 (public)，
保护 (protected) 和私有 (private) 的顺序进行。不要重新定义同一个可见性。
下面是一个类定义的例子：

``` cpp
    class FOO
    {
    public:
        FOO();
        void FooPublicMethod();

    protected:
        void fooProtectedMethod();

    private:
        void fooPrivateMethod();

        // Private not redefined here unless no private methods.
 ·      // 这里不需要重新定义私有(private) 除非上面没有私有方法
        int m_privateMemberVariable;
    };
```

## 4.12 Getters 和 Setters

为了提高可读性，没有其他附带作用的读写私有成员的类方法可以触犯一些通常的格式规则。
这种方法的所有成分都在一行里，方法之间没有空行：

``` cpp
public:
    wxString GetFoo() const { return m_foo; }
    void SetFoo( const wxString& aFoo ) { m_foo = aFoo; }
```

# 5. 许可声明

可以将 copyright.h 中的内容拷贝到你的新代码文件中，修改 \\author\\
字段。 KiCad
依靠版权法版权保护能力，也就是说源代码文件必须有版权，不能释放到公共域。
每一个源代码文件都有一到多个属主 (owner)。

# 6. 调试输出 (debugging output)

调试输出是验证代码的一个常用方法。但是他不应该在调试编译 (debug build)
中一直打开。
不然会让其他开发者很难看到他们自己调试输出，还会严重影响调试编译的性能。
当你需要使用调试输出时，使用
[wxLogDebug](https://docs.wxwidgets.org/3.0/group__group__funcmacro__log.html#ga9c530ae20eb423744f90874d2c97d02b)
， 不要使用 `printf` 或者 C++ 输出流。
如果你不小心把调试输出遗留在代码中， 他会在发布编译 (release builds)
中被扩展成空。在推送到 KiCad 仓库前，
所有的调试输出都应该被删除，不要简单地将调试输出注释起来。这样会使代码库积累下繁琐的内容。
如果你需要为以后的测试留下调试输出，那就应该用追踪输出 (tracing output),
参见 6.1 小节。

## 6.1 用追踪输出代替调试输出

有时候需要用调试数据来确保代码是否按照期望的那样工作了。这种情况下就需要用
[wxLogTrace](https://docs.wxwidgets.org/3.0/group__group__funcmacro__log.html#gae28a46b220921cd87a6f75f0842294c5)
。 这中方法可以允许调试输出被 `WXTRACE` 环境变量控制。当使用 wxLogTrace
时， 追踪环境变量字符串应当添加到 `trace_helper.{h/cpp}`
源代码文件中或者是本地使用 [Doxygen](https://www.doxygen.nl/index.html)
注释 `\ingroup trace_env_vars`。

# 7. 头文件

项目的 \*.h 源文件应该：

- 包括一个许可申明

- 包括一个嵌套包含 (nested include) `#ifndef`

- 完全独立，不依赖于其他没有包含在里面的头文件。

许可申明已经在之前详述了.

## 7.1 嵌套包含 (nested include) \#ifndef

每一个头文件都应该包含一个 `#ifndef`。
当这个头文件被多次输送给编译器时，它通常用来防止编译器报错。
在许可申明之后，文件的头部，应该有这样几行 (特定文件名标记不一定是
`RICHIO_H_`):

``` cpp
    #ifndef RICHIO_H_
    #define RICHIO_H_
```

在头文件的最后，用这样的一行：

``` cpp
    #endif // RICHIO_H_
```

`#ifndef` 包括了从许可申明之后开始直到文件的最后的内容。重要的是，
它应该也要包括 `#include` 语句，这样如果 `#ifndef`
是假时，编译器可以跳过这些文件， 就能节约很多编译时间。

## 7.2 没有未满足的依赖项的头文件

任何一个头文件都应该包含它所依赖的所有头文件。 （注意：KiCad
现在还没有完全做到这一点，但是这是项目的目标）

对项目中的任何一个头文件进行编译，如果正确地对编译器设置了包含文件路径，
那么这个头文件应该能够没有错误地正常编译。

**例如**

    $ cd /svn/kicad/testing.checkout/include
    $ g++ wx-config --cxxflags -I . xnode.h -o /tmp/junk

这样的头文件结构使得在客户 \*.cpp 文件包含一个项目头文件时，
不需要在这个头文件之前包含其他的项目头文件。 （客户 \*.cpp
文件是指准备使用，而不是实现这个头文件暴露的公开 API 的文件）。

不应该要求客户代码来粘接头文件所想要暴露的东西。
这个头文件应该被看作是使用这个头文件所暴露的 API 的 **凭证** 。

这里不是要指出暴露多少，而是说头文件想要暴露的东西，
应该只需要包含着一个头文件就能完全使用，而不需要再包含其他的头文件。

对于类的头文件和一个对它的实现 \*.cpp 文件的情况，最好按照现在的做法，
隐藏尽可能多的私有实现， 不需要暴露为公开 API 的头文件，应该在 \*.cpp
文件中包含。 总而言之，这一节主要要强调的是客户代码，
只需要包含一个头文件就可以使用这个头文件所暴露的所有 公共 API 了。

# 8. 当有疑问的时候…​

当编辑一个已经存在的代码文件时，如果有多个可以选择的代码格式选项或者没有已经定义好的格式，
那么应该遵循这个文件中已经存在的格式。

# 9. 在看到这篇文档前我已经写了 n 行代码了

没关系。 我们都会犯错。幸运的是，KiCad 提供了一个代码美化工具 uncrustify
的配置文件。 uncrustify
不会纠正你的几个具体问题，但是它在美化代码方面做得很好。 但是 uncrustify
在有些地方的缩进选择并不太理想， 并且在 wxT(“”) 和 \\(“”)
字符串申明宏作为其他函数的参数时比较纠结。 所以在 uncrustify
代码文件之后， 请检查是否有错误提示，并手工纠正错误。 可以在这里找到关于
uncrustify \[website\]\[uncrustify\] 的更多信息。

\[uncrustify\]: <http://uncrustify.sourceforge.net/>

# 10. 给我个例子

用一个例子就能更加一阵见血地说明问题。 下面的 richio.h
代码文件就是直接从 KiCad 的源代码里拷贝出来的。

``` cpp
    /*
     * This program source code file is part of KICAD, a free EDA CAD application.
     *
     * Copyright (C) 2007-2010 SoftPLC Corporation, Dick Hollenbeck <dick@softplc.com>
     * Copyright (C) 2007 KiCad Developers, see change_log.txt for contributors.
     *
     * This program is free software; you can redistribute it and/or
     * modify it under the terms of the GNU General Public License
     * as published by the Free Software Foundation; either version 2
     * of the License, or (at your option) any later version.
     *
     * This program is distributed in the hope that it will be useful,
     * but WITHOUT ANY WARRANTY; without even the implied warranty of
     * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
     * GNU General Public License for more details.
     *
     * You should have received a copy of the GNU General Public License
     * along with this program; if not, you may find one here:
     * http://www.gnu.org/licenses/old-licenses/gpl-2.0.html
     * or you may search the http://www.gnu.org website for the version 2 license,
     * or you may write to the Free Software Foundation, Inc.,
     * 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA
     */

    #ifndef RICHIO_H_
    #define RICHIO_H_


    // This file defines 3 classes useful for working with DSN text files and is named
    // "richio" after its author, Richard Hollenbeck, aka Dick Hollenbeck.


    #include <string>
    #include <vector>

    // I really did not want to be dependent on wxWidgets in richio
    // but the errorText needs to be wide char so wxString rules.
    #include <wx/wx.h>
    #include <cstdio>       // FILE



    /**
     * A class used to hold an error message and may be used to throw exceptions
     * containing meaningful error messages.
     */
    struct IOError
    {
        wxString    errorText;

        IOError( const wxChar* aMsg ) :
            errorText( aMsg )
        {
        }

        IOError( const wxString& aMsg ) :
            errorText( aMsg )
        {
        }
    };


    /**
     * Read single lines of text into a buffer and increments a line number counter.
     */
    class LINE_READER
    {
    protected:

        FILE*               fp;
        int                 lineNum;
        unsigned            maxLineLength;
        unsigned            length;
        char*               line;
        unsigned            capacity;

    public:

        /**
         * @param aFile is an open file in "ascii" mode, not binary mode.
         * @param aMaxLineLength is the number of bytes to use in the line buffer.
         */
        LINE_READER( FILE* aFile, unsigned aMaxLineLength );

        ~LINE_READER()
        {
            delete[] line;
        }

        /*
        int  CharAt( int aNdx )
        {
            if( (unsigned) aNdx < capacity )
                return (char) (unsigned char) line[aNdx];
            return -1;
        }
        */

        /**
         * Read a line of text into the buffer and increments the line number
         * counter.
         *
         * @return is the number of bytes read, 0 at end of file.
         * @throw IO_ERROR when a line is too long.
         */
        int ReadLine();

        operator char* ()
        {
            return line;
        }

        int LineNumber()
        {
            return lineNum;
        }

        unsigned Length()
        {
            return length;
        }
    };



    /**
     * An interface (abstract class) used to output ASCII text in a convenient way.
     *
     * The primary interface is printf() like with support for indentation control.
     * The destination of the 8 bit wide text is up to the implementer. If you want
     * to output a wxString, then use CONV_TO_UTF8() on it before passing it as an
     * argument to Print().
     * <p>
     * Since this is an abstract interface, only classes derived from this one
     * will be the implementations.
     * </p>
     */
    class OUTPUTFORMATTER
    {

    #if defined( __GNUG__ )   // The GNU C++ compiler defines this

    // When used on a C++ function, we must account for the "this" pointer,
    // so increase the STRING-INDEX and FIRST-TO_CHECK by one.
    // See http://docs.freebsd.org/info/gcc/gcc.info.Function_Attributes.html
    // Then to get format checking during the compile, compile with -Wall or -Wformat
    #define PRINTF_FUNC       __attribute__ ((format (printf, 3, 4)))

    #else
    #define PRINTF_FUNC       // nothing
    #endif

    public:

        /**
         * Format and write text to the output stream.
         *
         * @param nestLevel is the multiple of spaces to preceed the output with.
         * @param fmt is a printf() style format string.
         * @param ... is a variable list of parameters that will get blended into
         *  the output under control of the format string.
         * @return the number of characters output.
         * @throw IO_ERROR if there is a problem outputting, such as a full disk.
         */
        virtual int PRINTF_FUNC Print( int nestLevel, const char* fmt, ... ) = 0;

        /**
         * Return the quoting character required for aWrapee.
         *
         * Return the quote character as a single character string for a given
         * input wrapee string.  If the wrappee does not need to be quoted,
         * the return value is "" (the null string), such as when there are no
         * delimiters in the input wrapee string.  If you want the quote character
         * to be assuredly not "", then pass in "(" as the wrappee.
         * <p>
         * Implementations are free to override the default behavior, which is to
         * call the static function of the same name.
         * </p>
         *
         * @param aWrapee is a string that might need wrapping on each end.
         * @return the quote character as a single character string, or ""
         *   if the wrapee does not need to be wrapped.
         */
        virtual const char* GetQuoteChar( const char* aWrapee ) = 0;

        virtual ~OUTPUTFORMATTER() {}

        /**
         * Get the quote character according to the Specctra DSN specification.
         *
         * @param aWrapee is a string that might need wrapping on each end.
         * @param aQuoteChar is a single character C string which provides the current
         *          quote character, should it be needed by the wrapee.
         *
         * @return the quote_character as a single character string, or ""
         *   if the wrapee does not need to be wrapped.
         */
        static const char* GetQuoteChar( const char* aWrapee, const char* aQuoteChar );
    };


    /**
     * Implement an OUTPUTFORMATTER to a memory buffer.
     */
    class STRINGFORMATTER : public OUTPUTFORMATTER
    {
        std::vector<char>       buffer;
        std::string             mystring;

        int sprint( const char* fmt, ... );
        int vprint( const char* fmt,  va_list ap );

    public:

        /**
         * Reserve space in the buffer
         */
        STRINGFORMATTER( int aReserve = 300 ) :
            buffer( aReserve, '\0' )
        {
        }


        /**
         * Clears the buffer and empties the internal string.
         */
        void Clear()
        {
            mystring.clear();
        }

        /**
         * Remove whitespace, '(', and ')' from the internal string.
         */
        void StripUseless();


        std::string GetString()
        {
            return mystring;
        }


        //-----<OUTPUTFORMATTER>------------------------------------------------
        int PRINTF_FUNC Print( int nestLevel, const char* fmt, ... );
        const char* GetQuoteChar( const char* wrapee );
        //-----</OUTPUTFORMATTER>-----------------------------------------------
    };


    #endif // RICHIO_H_
```

# 11. 其他资源

互联网上有很多关于 C++ 注意事项和编程规范的优秀资源。下面是其中一些。
这些资源的编程规范虽然不一定符合 KiCad
的编程规范，但是包含有很多其它有用的信息。
而且，他们中的大部分还挺幽默的，读起来还很有趣。说不定，你就学到一些新招了。

- [C++ 编码标准](http://www.possibility.com/Cpp/CppCodingStandard.html)

- [Linux
  内核编码风格](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/Documentation/process/coding-style.rst)

- [C++
  运算符重载指南](http://www.cs.caltech.edu/courses/cs11/material/cpp/donnie/cpp-ops.html)

- [维基百科的编程风格页面](http://en.wikipedia.org/wiki/Programming_style)
