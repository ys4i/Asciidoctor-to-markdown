title: S-表达式 weight: 21 ---

S-表达式 是与 XML 相同的文本流或字符串，由一系列元素组成。
每个元素要么是原子，要么是列表。 原子对应于字符串，而列表对应于
s-表达式。

下面的语法代表了我们对 s-表达式 的定义：

    sexpr   ::= ( sx )
    sx      ::= atom sxtail | sexpr sxtail | NULL
    sxtail  ::= sx | NULL
    atom    :: quoted | value
    quoted  :: "ws_string"
    value   :: nws_string

原子可以是带引号的字符串
(由双引号括起来的包含空格的字符串)，也可以是不需要用引号引起来的非空格字符串。

KiCad 中使用的 s-expression 语法使用两种引用/语法策略， 这两种策略是由
Specctra DSN 规范的需求和我们自己的非 specctra 需求提供的。 Specctra DSN
规范在引用方面不是很清楚，最重要的是 Freerouter 的解释， 由于希望与
Freerouter 兼容，它实际上无论如何都会取代 Specctra DSN
规范中的任何内容。

我们有自己的需求，超出了 Specctra DSN
规范的需求，因此我们支持引用原子的两种语法或引用协议：

1.  Specctra 引用协议 (specctraMode)

2.  KiCad 引用协议 (non-specctraMode)

通过为非 Specctra DSN 文件单独定义模式，我们可以更好地控制自己的命运。

总而言之，在 specctraMode 中，Freerouter 告诉我们需要做什么。
在可以被认为是 KiCad 模式的非 specctraMode 中，我们有自己的引用协议，
可以在不破坏 specctraMode 的情况下进行更改。

在任一模式下，如何保存文件和如何读回文件之间需要达成一致，以满足往返要求。
使用一种模式写入的文件不一定可以使用另一种模式读取，尽管它可能是可读的。
只是别指望它了。

在 KiCad 模式下：

OUTPUTFORMATTER::Quoted() 是包装 s表达式 原子的工具。 DSNLEXER::NexTok()
基本上是逆函数，它读回令牌。
这两个必须达成一致，这样写出来的东西才能原封不动地送回来。

是否对字符串进行换行的决定留给了 `Quoted()` 函数。
如果字符串被换行，它还将转义内部\`双引号\`、`\n` 和 `\r`。
任何空字符串都用引号括起来，任何以 '#'
开头的字符串都用引号括起来，这样就不会与 s表达式 注释混淆。

# KiCad S-表达式 语法和引用协议 (非 specctraMode)：

1.  有些原子被认为是关键字，它们构成了叠加在 s-表达式 上的语法。
    所有关键字都是 ASCII 和小写。 这里不使用国际字符。

2.  所有 KiCad S-表达式 文件都使用 UTF8
    编码保存，并且应该支持原子中任何非关键字的国际字符。

3.  DSNLEXER::NextTok() 要求任何标记都在一行输入上。
    如果要保存多行字符串，Quoted() 将自动转义 \n 或 \r
    并将输出放在一行中。 往返应该没问题。

4.  带引号的字符串中只能有转义序列。
    转义序列允许外来工具在输入流中生成字节模式。 支持 C 样式 2
    字节十六进制代码，也支持 3 字节八进制转义序列。
    有关转义序列的完整列表，请参见 DSNLEXER::NextTok()，方法是在
    dsnlexper.cpp 文件中搜索字符串 “转义序列”。
    在应用转义处理之后，任何转义机制的使用仍必须生成 UTF-8 编码的文本。

5.  仅仅因为输入上支持转义序列，并不意味着 OUTPUTFORMATTER::Quoted()
    必须为输出生成这样的转义序列。 例如，在 S-表达式
    文件中有真的制表符是可以的。 因此，这将不会在输出时被转义。
    还有其他类似的案例。

6.  反斜杠是转义字节。
