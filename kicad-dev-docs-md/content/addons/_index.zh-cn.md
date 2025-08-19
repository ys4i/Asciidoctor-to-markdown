title: KiCad 插件 weight: 18 pre: "\<i class='fas
fa-chevron-right'\>\</i\> " ---

# 发布 KiCad 插件

本文档面向对创建可由 KiCad
插件和工具管理器安装的插件包感兴趣的用户和插件开发人员提供指南。

## 插件和工具管理器

插件和工具管理器 (PCM) 允许 KiCad 用户从公共或私有仓库发现并安装插件包。
这些插件包可能包含 Python 插件、符号或封装库、颜色主题和其他 KiCad
工具集。

KiCad 团队运营着一个官方的插件工具仓库，默认情况下 KiCad
用户可以使用该仓库。 用户还可以添加由第三方公开托管或私下托管的其他仓库
(例如，内部公司仓库)。

## 打包您的工具

一个 KiCad
插件包由一个压缩的压缩包文件组成，其中包含一个元数据文件、包内容和几个可选文件。
压缩包文件必须为 ZIP 格式 (与 ISO (国际标准化组织) 21320-1 标准兼容)。
压缩包文件的内容取决于包的类型，但所有包都必须包含一个名为
`metadata.json` 的控制文件，下面将详细介绍。

<div class="note">

插件压缩包必须遵循下面列出的确切文件夹结构。
与您的插件无关的额外文件是不允许的，必须从压缩包中删除。

</div>

### Python 插件

Python 插件压缩包的结构必须如下所示：

    Archive root // 压缩包根目录
    |- plugins // 插件文件
       |- __init__.py // python 脚本文件
       |- ...
    |- resources // 资源文件
       |- icon.png // png 图标文件
    |- metadata.json // json 元数据文件

`icon.png` 是一个可选的 64x64-pixel (像素) 图标，它将在 PCM
(插件和工具管理器) 对话框中显示在您的软件包旁边。 如果未提供图标，则
`resources` (资源文件) 子目录可能为空或被省略。

`plugins` 子目录的内容应该包括您的插件所需的任何 Python
源文件和资源文件。 将您的插件直接放在 `plugins`
子目录中，而不是放在二级子目录中。

<div class="note">

对于提供工具栏按钮图标的操作插件，该图标应该更小 (24x24)，并且位于
`plugins` 子目录中。 PCM 不会使用操作插件工具栏图标。

</div>

### 工具集库

工具库包的结构必须如下所示：

    Archive root // 压缩包根目录
    |- footprints // 封装文件
       |- first-library.pretty // 第一个封装库
          |- my-footprint.kicad_mod // 封装文件
          |- ...
       |- second-library.pretty // 第二个封装库
          |- another-footprint.kicad_mod // 封装文件
          |- ...
    |- 3dmodels // 3D 模型文件
       |- 3d-library.3dshapes // 3D 模型库
          |- my-model.stp // stp 3D 模型文件
          |- my-model.wrl // wrl 3D 模型文件
          |- ...
    |- symbols // 符号文件
          |- my-symbols.kicad_sym // 符号库
          |- ...
    |- resources // 资源文件
       |- icon.png // png 图标文件
    |- metadata.json // json 元数据文件

每个工具库包必须包含一个或多个 `封装`、`3D 模型` 或 `符号` 子目录。
这些子目录中的每一个都可以包含一个或多个相应类型的库。

`icon.png` 是一个可选的 64x64-pixel (像素) 图标，它将显示在 PCM
(插件和工具管理器) 对话框中的软件包旁边。 如果未提供图标，则 `resources`
(资源文件) 子目录可能为空或被省略。

### 配色主题

配色主题包的结构必须如下所示：

    Archive root // 压缩包根目录
    |- colors // 配色文件
       |- my-theme.json // json 配色主题文件
    |- resources // 资源文件
       |- icon.png // png 图标文件
    |- metadata.json // json 元数据文件

`icon.png` 是一个可选的 64x64-pixel (像素) 图标，它将显示在 PCM
(插件和工具管理器) 对话框中的软件包旁边。 如果未提供图标，则 `resources`
(资源文件) 子目录可能为空或被省略。
对于颜色主题，最好是图标以某种方式代表主题，例如，使用与主题相同的原色和背景色。

### 元数据文件格式

元数据文件 `metadata.json` 遵循 JSON 模式，目前发布在
<https://go.kicad.org/pcm/schemas/v1>

除非另有说明，否则所有字段都限制为有效的 UTF-8 码位和 1000 个字符。

配色主题示例 `metadata.json` ：

    {
    // 翻译人员注：进行了意思翻译便于理解和阅读，
    // 编写 metadata.json 按 https://go.kicad.org/pcm/schemas/v1 语法规范编写，
    // 如果有更新请参考最新的规范，中文翻译更新是落后于英文原文
        "$schema": "https://go.kicad.org/pcm/schemas/v1",
        "name": "My Beautiful Theme",
        // 插件名称 我的美丽主题
        "description": "A theme that makes KiCad truly beautiful",
        // 描述 一个让 KiCad 真正美丽的主题
        "description_full": "I came up with this theme in a dream one night.",
        // 完整描述 一天晚上，我在梦里想到了这个主题。
        "identifier": "com.github.kicad-user.kicad-beautiful-theme",
        // 标识符 com.github.kicad-user.kicad-beautiful-theme 域名逆序写法
        "type": "colortheme",
        // 类型 配色主题
        "author": {
        // 作者
            "name": "KiCad User",
            // 名称 KiCad 用户
            "contact": {
            // 联系方式
                "web": "https://mywebsite.com"
                // 网站 https://mywebsite.com
            }
        },
        "maintainer": {
        // 维护人员
            "name": "KiCad User",
            // 名称 KiCad 用户
            "contact": {
            // 联系方式
                "web": "https://mywebsite.com"
                // 网站 https://mywebsite.com
            }
        },
        "license": "CC0-1.0",
        // 许可证 CC0-1.0
        "resources": {
        // 资源文件
            "homepage": "https://github.com/kicad-user/kicad-beautiful-theme"
            // 主页 https://github.com/kicad-user/kicad-beautiful-theme
        },
        "versions": [
        // 版本
            {
                "version": "1.0",
                // 版本 1.0
                "status": "stable",
                // 状态 稳定版
                "kicad_version": "5.99",
                // kicad 版本 5.99
                "download_sha256": "YOUR_SHA256_HERE",
                // 下载文件的 sha256 校验值
                "download_size": 1234,
                // 下载文件的大小
                "download_url": "https://github.com/YOUR/DOWNLOAD/URL/kicad-beautiful-theme.zip",
                // 下载文件的 url
                "install_size": 5678
                // 安装大小
                // 注 json 文件最后一行定义是没有 `,`
            }
        ]
    }

#### 必填字段

`$schema`: 必须包含指向当前 KiCad 插件 JSON 架构的 URL
(<https://go.kicad.org/pcm/schemas/v1>)

`name (插件名称)`: 软件包的人类可读名称 (可以包含任何有效的 UTF-8 字符)

`description (描述)`: 程序包的简短自由格式描述，将在 PCM
中与程序包名称一起显示。 最多可以包含 150 个字符。

`description_full (完整描述)`: 当用户选择包时，将在 PCM
中显示的包的自由格式的长描述。 可能包括换行符。

`identifier (唯一标识符)`: 包的唯一标识符。只能包含字母数字字符和破折号
(`-`)。 长度必须介于 2 到 50 个字符之间。
必须以拉丁字符开头，以拉丁字符或数字结尾。
有关选择标识符的指导原则，请参阅下面关于命名空间的结构。

`type (类型)`: 包的类型，可以是
`plugin (插件)`、`Library (库)`、`colortheme (配色主题)` 中的一个。

`author (作者)`: 包含一个必填字段 `name (名称)`
的对象，其中包含包创建者的名称。 可以存在可选的 `contact (联系方式)`
字段，该字段包含具有联系信息的自由格式字段。

`maintainer (维护人员)`: 与 `author` 相同，但包含包的维护人员信息。

`license (许可证)`: 一个字符串，包含分发包所依据的许可证。

许可证必须是符合 [Debian
许可证规则](https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/#license-specification)
的有效字符串，并进行以下修改：

- MIT (麻省理工学院) 的许可证总是被认为意味着 [Expat
  许可证](https://www.debian.org/legal/licenses/mit).

- 没有版本号的 CC (知识共享)
  许可证是允许的，这表明作者没有指定适用的版本。

- Stripping of trailing zeroes is not recognized.
  (待定翻译：不识别尾随零的剥离。)

- `CERN-OHL` 被认为是有效的许可证。

- `WTFPL` 被认为是有效的许可证。

- `Unlicense` 被认为是有效的许可证。

以下许可字符串也是有效的，并指示上述未描述的其他许可：

- `open-source (开源)`: 其他开放源码倡议 (OSI) 批准的许可证。

- `unrestricted (无限制)`: 不是 OSI 批准的许可证，但不受限制。

`versions (版本)`: 描述包版本的对象列表。每个版本对象可以包含以下键：

`version (版本)`: 包含包版本的字符串 (格式由您决定)。

`status (状态)`: 包含以下内容之一的字符串：

- `stable (稳定版)`: 这个软件包很稳定，可以普遍使用。

- `testing (测试版)`: 这个软件包正处于测试阶段，用户应谨慎并报告问题。

- `development (开发版)`:
  这个软件包还处于开发阶段，不能指望它能完全发挥作用。

- `deprecated (弃用版)`: 这个软件包不再维护。

- `invalid (无效版)`: 这个软件包不再可用。

`kicad_version ( KiCad 版本)`: 这个软件包所需的最低 KiCad 版本 (major
(主要版本).minor (次要版本))。

`kicad_version_max (KiCad 最新版本)` (可选): 这个软件包兼容的最新 KiCad
版本 (major (主要版本).minor (次要版本))。

`download_sha256 (sha256 校验值)`: 包含这个软件包的压缩包的 SHA-256
哈希的校验字符串。

`download_url (下载链接)`: 包含这个软件包的压缩包的直接下载 URL
的字符串。

`download_size (压缩包大小)`: 这个软件包的压缩包的大小 (以字节为单位)。

`install_size (安装后大小)`: 这个软件包的大小 (未压缩)，(以字节为单位)。

<div class="note">

`download_*` 键只能出现在您提交给包元数据仓库的 `metadata.json` 版本中，
而不能出现在这个软件包的压缩包中实际存在的文件版本中。 无法在压缩包内的
`metadata.json` 文件中放入有效的 `download_sha256` 值。

</div>

TODO: 描述可选字段

## 提交给官方仓库

KiCad 托管一个官方的插件仓库，默认情况下所有 KiCad
用户都可以使用该仓库。
要包含在官方仓库中，您的包必须满足上述技术要求之外的几个要求。

### 命名空间和命名

- 您的包标识符 **必须** 使用逆序 DNS 格式命名。 例如，官方 KiCad
  插件使用 `org.kicad.Packagename` 命名空间。

- 如果您的插件工具托管在公共可见的代码托管服务 (如 GitHub 或 GitLab )
  上，则您的命名空间应该基于此服务。
  例如，`com.github.username.Packagename`。
  如果软件包和仓库之间存在一对一的对应关系，则软件包标识符通常应该与仓库名称匹配。

- 您的软件包命名空间也可能基于您掌握的域名。
  如果您提交的域名与软件包的下载位置之间没有明显的联系，或者不清楚您是否掌握了该域名，
  KiCad 团队可能会在批准您的提交之前要求您提供更多信息。

- 您的包标识符 **必须**
  是唯一的。上面的命名空间要求应该会使这一点变得简单。

### 许可证

- 软件包 **必须**
  在有效的开源许可证下获得许可。封闭源码软件包可以在第三方仓库下与 KiCad
  一起使用， 但官方 KiCad
  仓库中的所有软件包都必须是开源的，以允许代码审查、问题报告，并保持与
  KiCad 本身的许可证兼容性。

- Packages containing code (Python plugins) **must** be licensed under
  an open-source license [compatible with the GNU
  GPL](https://www.gnu.org/licenses/license-list.en.html#GPLCompatibleLicenses).
  包含代码的软件包 ( Python 插件) **必须** 在具有 [与 GNU
  GPL兼容](https://www.gnu.org/licenses/license-list.en.html#GPLCompatibleLicenses)
  的开放源码许可证。

- 包含数据 (配色主题、库等) 的软件包应按照 CC (知识共享)
  或类似许可进行许可。

### 技术要求

- 提交到官方仓库的元数据文件 **必须** 在元数据中包含 `download_sha256`
  校验值，该校验值包含压缩包的有效 SHA-256 哈希校验值。

- `download_url`**必须** 指向可公开访问的 URL。

- 软件包元数据 **必须** 为英文。包内容 (例如，Python
  插件创建的对话框中使用的语言) 可以是任何语言，
  但是软件包描述应该清楚地说明内容是否是英语以外的语言。目前，KiCad
  还没有允许插件提供多语言翻译的内置机制。

- 软件包源 **必须** 托管在允许问题报告和跟踪的位置。符合此要求的示例包括
  GitHub、GitLab、Bitbucket 和 Sourceforge。

### 内容策略

- 添加到官方 KiCad 插件仓库的包 **必须** 遵循我们的社区行为准则
  [行为规范](https://www.kicad.org/contribute/code-of-conduct/)。 KiCad
  团队保留查看包内容和元数据并拒绝提交的权利。违反这一行为准则的人。

- KiCad
  团队不保证任何插件内容的质量、安全性或安全性，但会努力保持安全的一般标准。
  如果与软件包相关的安全、安全或隐私问题引起我们的注意，我们保留采取纠正措施的权利，包括在不事先通知的情况下从仓库中删除软件包。
  在这种情况下，一旦问题得到了令 KiCad
  团队满意的解决，软件就可以再次提交到仓库。

- KiCad 团队保留将来修改或扩展此政策的权利，以便最好地满足 KiCad
  用户社区的需求。
  违反新的或更新的内容策略的现有软件包的发布者将收到提前通知，并有机会进行更改以满足更新的策略。

### 商业服务

- 链接到或提供商业服务 (包括但不限于 PCB
  制造、元件查找和订单管理)的软件包，**必须** 首先通过
  <plugins@kicad.org> 与 KiCad 团队联系，讨论商业插件选项。

## 提交您的软件包

一旦您按照上面的指导创建了一个软件包，并确认您的软件包可以使用元数据文件中列出的
URL 下载，您就可以将您的软件包提交到官方的 KiCad 插件仓库。

为此，您必须拥有 GitLab 的帐户，并将元数据文件作为合并请求提交到
<https://gitlab.com/kicad/addons/metadata> 的包元数据仓库。 在
`packages (软件包)` 目录中创建一个与您的 `package (软件包)`
标识符同名的目录，该目录包含 `metadata.json`
文件和任何其他可选的元数据文件 (例如，图标)，如上所述。
只要包共享一个命名空间，您就可以在一个合并请求中提交多个新软件包。

<div class="note">

不要将合并请求提交到 <https://gitlab.com/kicad/addons/repository>
上的面向公众的仓库 - 对此仓库的更改将根据对元数据仓库的更改自动进行。

</div>

## 正在更新您的软件包

更新应作为更改 `metadata.json` 文件 (以及任何其他需要更新的程序包文件)
的附加合并请求提交。
只要包共享命名空间，您就可以在单个合并请求中提交对多个软件包的更新。
