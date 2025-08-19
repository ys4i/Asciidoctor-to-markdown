title: 设置框架 weight: 10 ---

设置框架管理应用程序设置以及工程。
本文档解释了如何使用该框架以及它的一些内部工作原理。

# 源代码指南

相关代码大部分在 `common/settings` 和 `common/project` 中。

| C++ 类                   | 描述                                                      |
|--------------------------|-----------------------------------------------------------|
| `SETTINGS_MANAGER`       | 加载和卸载设置文件和工程                                  |
| `JSON_SETTINGS`          | 所有设置对象的基类。表示单个 JSON 文件。                  |
| `NESTED_SETTINGS`        | 存储在另一个 (即没有自己的文件) 中的 `JSON_SETTINGS` 对象 |
| `PARAM`                  | 参数：用于在 `JSON_SETTINGS` 中存储数据的 helper 类       |
| `APP_SETTINGS_BASE`      | 应用程序 (框架) 设置的基类                                |
| `COLOR_SETTINGS`         | 设计用于存储颜色主题的 `JSON_SETTINGS` 的子类             |
| `COMMON_SETTINGS`        | 适用于 KiCad 每个组件的设置                               |
| `PROJECT_FILE`           | 表示工程 (`.kicad_pro`) 文件的 `JSON_SETTINGS`            |
| `PROJECT_LOCAL_SETTINGS` | 表示工程本地状态 (`.kicad_prl`) 文件的 `JSON_SETTINGS`    |

# 存储设置的位置

设置可能存储在四个主要位置：

1.  在 `COMMON_SETTINGS` 中：这是 KiCad 所有部分共享的设置。

2.  在应用程序设置对象 (`APP_SETTINGS_BASE` 的子类) 中。 这些对象 (如
    `EESCHEMA_SETTINGS` 和 `PCBNEW_SETTINGS`) 存储特定于 KiCad
    一部分的设置。
    具体地说，这些对象是在其各自应用程序的上下文中编译的，因此它们可以访问可能不属于
    `common` 的数据类型。

3.  在 `PROJECT_FILE` 中，它们将特定于加载的工程。 在 Board/Schematic
    Setup (电路板/原理图设置) 对话框中的大多数设置都是如此。 目前 KiCad
    只支持一次加载一个工程，代码中的很多地方都希望有一个 `PROJECT`
    对象始终可用。 因此，即使用户没有加载任何工程，`SETTINGS_MANAGER`
    也会始终确保有一个 "dummy" 的 `Project_FILE` 可用。
    此虚拟工程可以在内存中修改，但不能保存到磁盘。

4.  在 `PROJECT_LOCAL_SETTINGS` 对象中，它们将特定于加载的工程。
    该文件用于 “本地状态” 的设置，例如哪些板层是可见的， 这些设置不应该
    (至少对许多用户而言) 检出到源代码管理中。
    这里的任何设置都应该是暂时的，这意味着如果删除整个文件不会有不良影响。

# JSON_SETTINGS

`JSON_SETTINGS` 类是设置基础结构的主干。 是
`thirdparty/nlohmann_json/nlohmann/json.hpp` 提供的
`nlohmann::json::basic_json` 类的子类。 因此，任何时候需要对底层 JSON
数据进行原始操作时，都可以使用 [标准 nlohmann::json
API](https://nlohmann.github.io/json/api/basic_json/)。 JSON 内容表示
**文件在磁盘上的状态**，而不是向 C++ 公开的数据的状态。
两者之间的同步是通过参数(见下文)完成的，并且在从磁盘加载之后和保存到磁盘之前立即发生。

# 参数

参数建立 C 数据和 JSON 文件内容之间的链接。 一般情况下，参数由
\*\*路径\*\*、\*\*指针\*\* 和 \*\*默认值\*\* 组成。 路径是 \`“x.y.z”\`
形式的字符串，其中每个组件代表一个嵌套的 JSON 字典键。 指针是指向 C
成员变量的指针，该变量保存 `JSON_SETTINGS` 的使用者可访问的数据。
默认值用于在 JSON 文件中缺少数据时更新指针。

参数是 `include/settings/parameters.h` 中 `PARAM_BASE` 的子类。
创建了许多有用的子类，使在 JSON 文件中存储复杂数据变得更容易。

基本的 `PARAM` 类是模板化的，用于存储任何可以自动序列化为 JSON
的数据类型。 `PARAM` 的基本实例化可能如下所示：

        m_params.emplace_back( new PARAM<int>( "appearance.icon_scale",
                                               &m_Appearance.icon_scale, 0 ) );

这里，`m_Appearance.icon_scale` 是设置对象 (`struct` 内部的 `int`)
的公共成员。 `"appearance.icon_scale"` 为 JSON 文件中的
**路径**，默认为\`0\`。这将导致 JSON 如下所示：

        {
            "appearance": {
                "icon_scale": 0
            }
        }

请注意，自定义类型可以与 `PARAM<>` 一起使用，只要定义了 `to_json` 和
`from_json` 即可。 有关这方面的示例，请参阅 `COLOR4D`。

对于存储复杂的数据类型，有时使用 `PARAM_LAMBDA<>` 最简单，它允许您将
"getter" 和 "setter" 定义为参数定义的一部分。 您可以使用它来构建一个
`nlohmann::json` 对象，并将其存储为您的参数的 "value"。
有关实现方法的示例，请参阅 `NET_SETTINGS`。

# NESTED_SETTINGS

`NESTED_SETTINGS` 类类似于 `JSON_SETTINGS`，但它使用另一个
`JSON_SETTINGS` 对象作为后备存储器的文件。 `NESTED_SETTINGS`
的全部内容作为特定键的值存储在父文件中。 这有两个主要好处：

1.  您可以将大型设置拆分成更易于管理的部分

2.  您可以向父设置对象隐藏有关嵌套设置的知识

例如，工程文件的很多部分都作为 `NESTED_SETTINGS` 对象存储在
`PROJECT_FILE` 中。 这些对象包括 `SCHEMATIC_SETTINGS`、`NET_SETTINGS` 和
`BOARD_DESIGN_SETTINGS`， 它们被编译为 eesschema 或 pcbnew
的一部分，因此它们可以访问公共不可用的数据类型 (其中编译了
`PROJECT_FILE`)。

加载外部文件时，嵌套设置的所有数据都在底层的 `nlohmann::json`
数据存储中 — 只是在加载适当的 `NESTED_SETTINGS` 之前不会使用它。

`NESTED_SETTINGS` 对象的生命周期可以比父对象短。
这是必需的，因为在某些情况下 (例如使用项目文件)， 父级可以驻留在一个帧中
(例如 KiCad 管理器)，而可以创建和销毁其中使用嵌套设置的帧。
当嵌套设置被销毁时，它会确保将其数据存储到父级的 JSON 数据中。
如果需要，可以将父 `JSON_SETTINGS` 保存到磁盘。

# 架构版本和迁移

设置对象有一个 **架构版本**，它是一个常量整数，在需要迁移时可以递增。
将代码中的模式版本与加载的文件中的模式版本进行比较，如果文件版本较低
(较旧)，则运行 **迁移** 以使文件中的数据保持最新。

**迁移** 是负责从一个架构版本到另一个架构版本进行必要更改的函数。
它们在参数加载之前作用于 **底层 JSON 数据**。

更改设置文件时并不总是需要迁移。
您可以自由添加或删除参数，而无需更改模式版本或编写迁移。
如果添加参数，它们将被添加到 JSON 文件并初始化为其默认值。
如果删除参数，它们将在下次保存设置时从 JSON 文件中静默删除。
只有在需要根据当前状态对 JSON 文件进行更改时，才需要迁移。
例如，如果您决定重命名设置键，但希望保留用户的当前设置。

如果需要对设置文件进行 “突破性更改”，请执行以下操作：

1.  增加模式版本

2.  编写迁移，对底层的 `nlohmann::json` 对象进行必要的更改

3.  在 object 的构造函数中调用 `JSON_SETTINGS::registerMigration`
