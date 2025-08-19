title: UI 规范 weight: 10 summary: 有关如何实现用户界面元素的规则 ---

# 用户界面指南

本文档定义了 KiCad 中用户界面开发的指导原则。 开发人员在向 KiCad
项目贡献用户界面代码时应尽可能遵循这些指导原则。

## 文本大写

对于 KiCad 中使用的所有可见文本，请遵循中的建议。 [GNOME
用户界面指南的写作风格部分](https://developer.gnome.org/hig/stable/writing-style.html.en)
中的大写部分。 这适用于所有菜单、标题、标签、工具提示、按钮等。

应用程序名称的大写是 KiCad、Eesschema、CvPcb、GerbView 和 Pcbnew。
所有具有用户可见的应用程序名称的字符串都应按此方式大写。
在源代码注释中使用这种大写也是一个好主意，以防止混淆新的贡献者。

## 大写样式

GNOME 用户界面元素中使用了两种大写形式：标题大写和句子大写。
本节定义了大写样式以及应在何时使用每种类型的大写。

### 标题大写

使用标题大写时，除以下例外情况外，所有单词均为大写： \* 文章: a, an,
the. \* 连词: and, but, for, not, so, yet …​ \*
三个或三个以下字母的介词： at, for, by, in, to …​

### 句子大写

当句子大写时，第一个单词的第一个字母大写，以及其他任何通常在句子中大写的单词，如专有名词。

## 大写单词表

下表指出了每种类型的用户界面元素要使用的大写样式。

| 元素                                                          | 样式            |
|---------------------------------------------------------------|-----------------|
| Check box labels (复选框标签)                                 | Sentence (句子) |
| Command button labels (命令按钮标签)                          | Header (标题)   |
| Column heading labels (列标题标签)                            | Header (标题)   |
| Desktop background object labels (桌面背景对象标签)           | Header (标题)   |
| Dialog messages (对话框消息)                                  | Sentence (句子) |
| Drop-down combination box labels (下拉组合框标签)             | Sentence (句子) |
| Drop-down list box labels (下拉列表框标签)                    | Sentence (句子) |
| Field labels (字段标签)                                       | Sentence (句子) |
| Text on web pages (网页上的文本)                              | Sentence (句子) |
| Group box and window frame labels (组框和窗口框架标签)        | Header (标题)   |
| Items in drop-down and list controls (下拉列表控件中的项)     | Sentence (句子) |
| List box labels (列表框标签)                                  | Sentence (句子) |
| Menu items (菜单项)                                           | Header (标题)   |
| Menu items in applications (应用程序中的菜单项)               | Header (标题)   |
| Menu titles in applications (应用程序中的菜单标题)            | Header (标题)   |
| Radio button labels (单选按钮标签)                            | Sentence (句子) |
| Slider labels (滑块标签)                                      | Sentence (句子) |
| Spin box labels (数字显示框标签)                              | Sentence (句子) |
| Tabbed section titles (选项卡式部分标题)                      | Header (标题)   |
| Text box labels (文本框标签)                                  | Sentence (句子) |
| Titlebar labels (标题栏标签)                                  | Header (标题)   |
| Toolbar button labels (工具栏按钮标签)                        | Header (标题)   |
| Tooltips (工具提示)                                           | Sentence (句子) |
| Webpage titles and navigational elements (网页标题和导航元素) | Header (标题)   |

# 对话框

本节定义应如何设计对话框。KiCad 项目使用 [GNOME
用户界面指南](https://developer.gnome.org/hig/stable/) 来布局对话框。
设计对话框时，请遵循 [\[GNOME
用户界面指南的可视化布局部分](https://developer.gnome.org/hig/stable/visual-layout.html.en)。
KiCad 的对话框可以用
[wxformbuilder](https://github.com/wxFormBuilder/wxFormBuilder)
设计，也可以手工创建。
但是，现有对话框必须以与其实现时相同的方式进行维护。 请使用
[wxformbuilder-releases](https://github.com/wxFormBuilder/wxFormBuilder/releases)
以避免开发人员之间的版本不匹配。

## 退出键终止

请注意，仅当存在使用 ID wxID_Cancel
定义的对话框按钮，或者在对话框初始化期间调用使用 [wxDialog::SetEscapeID(
MY_ESCAPE_BUTTON_ID
)](http://docs.wxwidgets.org/3.0/classwx_dialog.html#a585869988e308f549128a6a065f387c6)
设置退出按钮 ID 时，退出键终止才能正常工作。
前者是处理退出键对话终止的首选方法。 WxFormBuilder 中有一个用于设置
“默认” 控件的复选框，这是按下 “Enter” 键时触发的复选框。

## 使用 Sizers 的对话框布局

在所有对话框中使用wxWidget "sizers"，不管它们有多简单。 在 KiCad
中禁止在对话框中使用绝对大小调整。 有关使用 sizers 的更多信息，请参见
[wxWidgets sizers
概述](http://docs.wxwidgets.org/3.0/overview_sizer.html)。 配置
sizers，以便在对话窗口展开时，sizers
自动对增加的对话窗口进行最合理的使用。 例如，在 Pcbnew 的 DRC
对话框中，应使用 sizers 来展开文本控件以使用全部可用的自由窗口区域，
以便用户在展开对话框窗口时最大化文本控件中项目的视图，从而更容易阅读更多
DRC 错误消息。 在没有一个组件比其他组件更重要的其他对话框中，可以将
sizers 配置为将控件定位到靠近。
对话框越变越大，不一定会将它们紧密捆绑在一起。
该对话框在大到足以显示所有用户界面元素的任何大小下都应该看起来很漂亮。

如果可能，请避免定义初始对话框大小。 让他们做好本职工作吧。
对话框适合大小调整后，请将最小大小设置为当前大小，以防止在调整对话框大小时遮挡对话框控件。
如果对话框控件的标签或文本是，请在运行时设置或更改。 重新运行
wxWindow::Fit() 以允许对话框调整大小并根据新的控件宽度进行调整。
这都可以在创建对话框之后、显示对话框之前完成，也可以根据需要使用类方法调整对话框大小。
将最小大小重置为更新后的对话框大小。

以 13 磅字体显示时，对话框窗口不应超过 1024 x 768。
请注意，最终用户使用的字体不是您可以从对话框中控制的，但出于测试目的，如果用户选择的字体大小为
13 磅， 请不要超过此对话框大小。如果您的对话框超出此限制，
请使用选项卡或其他分页方法重新设计对话框，以减小对话框的大小。

## 基类对话框

KiCad 项目有一个基类，大多数对话框 (如果不是所有对话框)
都应该派生自该基类。 使用 wxFormBuilder 时，请在 “对话框”
选项卡中添加以下设置：

- subclass.name ← DIALOG_SHIM

- subclass.header ← dialog_shim.h

这将覆盖 Show( bool ) wxWindow() 函数，并提供会话的保留大小和位置。
有关更多信息， 请参见 [DIALOG_SHIM
类源代码](https://gitlab.com/kicad/code/kicad/-/blob/master/common/dialog_shim.cpp)。

使用工具提示解释每个非明显控件的功能。
这一点很重要，因为帮助文件和维基经常落后于源代码。

## 在控件之间传输数据

对话框初始化时，对话框数据必须传输到对话框控件， 并在通过默认肯定操作
(通常单击 wxID_OK 按钮) 或单击 wxID_Apply 按钮关闭对话框时从控件传输。
WxWidgets 对话框框架通过使用验证器对此提供支持。 请阅读 [wxWidgets
文档](http://docs.wxwidgets.org/3.0/) 中的 [wxValidator
概述](https://docs.wxwidgets.org/3.0/overview_validator.html)。
在过去，数据传输是在各种默认按钮处理程序中处理的，几乎所有的默认按钮处理程序都被破坏了。
不要在对话框代码中实现默认按钮处理程序。
使用验证器在控件之间传输数据，并允许默认对话框按钮处理程序按设计方式工作。

### 国际化

要生成对话框中出现的字符串列表，需要在项目属性中启用
'internationalize'(国际化) 复选框。 否则，将无法翻译该对话框。

# 字符串引号

通常会将文本字符串加引号以供显示，这些文本字符串可用于呈现 HTML
的控件中。 使用尖括号会给 HTML 呈现控件带来麻烦，所以文本应该用单引号 ''
引起来。 例如：

- 'filename.kicad_pcb'

- 'longpath/subdir'

- 'FOOTPRINTNAME'

- 'anything else'
