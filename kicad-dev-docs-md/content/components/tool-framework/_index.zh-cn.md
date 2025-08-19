title: 工具框架 weight: 18 ---

本文档简要概述了 GAL 画布中工具系统的结构。

# 介绍

GAL (图形抽象层) 框架提供了一种强大的方法，可以轻松地向 KiCad 添加工具。
与旧的 “遗留” 画布相比，GAL 工具更灵活、更强大，也更容易编写。

GAL “工具” 是提供一个或多个要执行的 “操作” 的类。
动作可以是简单的一次性动作(例如 “放大” 或 “翻转对象” )，
也可以是交互过程(例如 “手动编辑多边形点”)。

Pcbnew GAL 中的一些工具示例如下：

- 选择工具 - "normal" 工具。此工具进入一种状态，
  在该状态下可以将项目添加到选定对象的列表中，
  然后其他工具即可对其执行操作。 (pcbnew/tools/selection_tool.cpp,
  pcbnew/tools/selection_tool.h)

- 编辑工具 - 此工具在组件被 "picked up" 时处于活动状态，
  并跟踪鼠标位置以允许用户移动组件。 编辑的各个方面 (例如翻转)
  也可由热键或其他工具使用。
  (pcbnew/tools/edit_tool.cpp,pcbnew/tools/edit_tool.h)

- 绘图工具 - 此工具控制绘制图形元素 (如线段和圆) 的过程。
  (pcbnew/tools/drawing_tool.cpp,pcbnew/tools/drawing_tool.h)

- 缩放工具 - 允许用户放大和缩小。

# 工具的主要部件

工具有两个主要方面：操作和工具类。

## 工具操作

`TOOL_ACTION` 类充当 GAL 框架调用工具提供的操作的句柄。
一般来说，每个操作，不管是交互的还是非交互的，都有一个 `TOOL_ACTION`
实例。 这提供了：

- 名称，格式为 `pcbnew.ToolName.actionName`，内部用于分配操作

- “范围”，确定工具何时可用：

- 当操作特定于特定工具时，返回 `AS_CONTEXT`。
  例如，`pcbnew.InteractiveDrawing.incWidth`
  会在线条仍在绘制时增加线条的宽度。

- `AS_GLOBAL`，当工具总是可以通过热键调用时，或者在执行其他工具时。
  例如，在交互式选择过程中，可以从选择工具的菜单访问缩放操作。

- “默认热键”，当用户没有提供自己的配置时使用。

- “菜单项”，即从菜单访问此工具时显示的(可翻译的)字符串。

- “菜单描述”，它是菜单项工具提示中显示的字符串，并在需要时提供更详细的描述。

- 一个“图标”，显示在菜单和操作按钮上

- “标志” 包括：

- `AF_ACTIVATE`，表示工具进入活动状态。当工具处于活动状态时，
  它将不断接收 UI 事件，如鼠标单击或按键， 这些事件通常在事件循环中处理
  (请参阅 TOOL_INTERACTIVE::Wait())。

- 一个参数，它允许不同的操作以不同的效果调用相同的函数，例如 “左一步” 和
  “右一步”。

## 工具类

GAL 工具继承了 `Tool_BASE` 类。 Pcbnew 工具一般继承自 `PCB_TOOL`，即
`TOOL_INTERACTIVE`，即 `TOOL_BASE`。 Eeschema 工具直接继承自
`TOOL_INTERACTIVE`。

工具的工具类可以是相当轻量级的 - 很多功能都是从工具的基类继承而来的。
这些基类提供了对几项内容的访问，特别是：

- 访问父框架 (一个 `wxWindow`，可用于修改视口、设置光标和状态栏内容等)。

- 使用函数 `getEditFrame<T>()`，其中 `T` 是您想要的 frame 子类。 在
  `PCB_TOOL` 中，可能是 `PCB_EDIT_FRAME`。

- 访问 `TOOL_MANAGER`，该 `TOOL_MANAGER` 可用于访问其他工具的操作。

- 访问支持该工具的 “模型” (某种 `EDA_ITEM`)。

- 使用 `getModel<T>()` 访问。在 `PCB_TOOL` 中，型号类型 `T` 为 `BOARD`，
  可用于访问和修改 PCB 内容。

- 访问 `KIGFX::VIEW` 和 `KIGFX::VIEW_CONTROLS`，用于操作 GAL 画布。

工具实现的主要部分是 `TOOL_MANAGER` 用于设置和管理工具的函数：

- 构造函数和析构函数来建立所需的任何类成员。

- TOOL_BASE 类需要传递一个字符串作为工具名称， 通常类似于
  `pcbnew.ToolName`。

- `Init()` 函数 (可选)，通常用于填写属于此工具的上下文菜单，
  或访问其他工具的菜单并向其中添加项目。 当工具注册到工具管理器时，
  此函数被调用一次。

- `Reset()` 函数，在重新加载模型 (例如，`BOARD`) 时、 切换 GAL
  画布时以及在工具注册之后调用。 必须在此函数中释放从 GAL
  视图或模型声明的任何资源， 因为它们可能会变为无效。

- `setTrantions()` 函数，该函数将工具操作映射到工具类中的函数。

- 调用操作时要调用的一个或多个函数。
  如果需要，许多操作都可以调用相同的函数。 这些函数具有以下签名：

- int TOOL_CLASS::FunctionName( const TOOL_EVENT& aEvent )

- 返回 `0` 表示成功。

- 这些函数由 `TOOL_MANAGER` 在关联事件到达时调用 (关联是通过
  TOOL_INTERACTIVE::Go() 函数创建的)。

- 它们通常可以是私有的，因为它们不是由任何其他代码直接调用的，
  而是由工具管理器的协程框架根据 `setTrantions()` 映射调用的。

## 交互式操作

交互式动作的动作处理程序在循环中处理来自工具管理器的重复动作，
直到指示工具应该退出的动作为止。

交互式工具通常还会通过光标更改和设置状态字符串来指示它们处于活动状态。

``` cpp
    int TOOL_NAME::someAction( const TOOL_EVENT& aEvent )
    {
        auto& frame = *getEditFrame<PCB_EDIT_FRAME>();

        // 设置工具提示和光标 (实际上看起来像十字准线)
        frame.SetToolID( ID_LOCAL_RATSNEST_BUTT,
                wxCURSOR_PENCIL, _( "Select item to move left" ) );
        getViewControls()->ShowCursor( true );

        // 激活该工具，现在它将是第一个接收事件的工具。
        // 如果您正在为单个操作 (例如放大) 编写处理程序，
        // 那么它将是第一个接收事件的工具，
        // 而不是需要更多事件才能操作 (例如拖动组件) 的交互式工具。

        Activate();

        // 主事件循环
        while( OPT_TOOL_EVENT evt = Wait() )
        {
            if( evt->IsCancel() || evt->IsActivate() )
            {
                // 交互式工具结束
                break;
            }
            else if( evt->IsClick( BUT_LEFT ) )
            {
                // 在这里做点什么
            }
            // 其他事件...
        }

        // 将印刷电路板框架重置为我们拿到它时的状态
        frame.SetToolID( ID_NO_TOOL_SELECTED, wxCURSOR_DEFAULT, wxEmptyString );
        getViewControls()->ShowCursor( false );

        return 0;
    }
```

## 工具菜单

顶级工具 (即用户直接输入的工具) 通常提供其自己的上下文菜单。
仅从其他工具的交互模式调用的工具会将其菜单项添加到这些工具的菜单中。

要在顶级工具中使用 `TOOL_MENU`，只需添加一个作为成员，
并在构造时引用工具进行初始化：

``` cpp
class TOOL_NAME: public PCB_TOOL
{
public:
    TOOL_NAME() :
        PCB_TOOL( "pcbnew.MyNewTool" ),
        m_menu( *this )
    {}

private:
    TOOL_MENU m_menu;
}
```

然后，您可以添加菜单访问器，
或提供自定义函数以允许其他工具添加您认为合适的任何其他操作或子集。

然后，您可以通过调用 `m_menu.ShowContextMenu()`
从交互式工具循环调用菜单。 单击此菜单中的工具条目将触发操作 -
在工具的事件循环中不需要进一步的操作。

# 提交对象

`COMMIT` 类管理对 `EDA_ITEMS` 的更改，
该更改将任意数量的项目的更改合并到单个撤消/重做操作中。 编辑 PCB 时，对
PCB 的更改由派生的 `BOARD_COMMIT` 类管理。

该类以 `PCB_BASE_FRAME` 或 `PCB_TOOL` 作为参数。 对于 GAL 工具，使用
`PCB_TOOL` 更合适， 因为如果不需要，则不需要查看 frame 类。

提交的过程是：

- 构造适当的 `COMMIT` 对象

- 在修改任何项之前，请使用 `Modify( item )` 将其添加到提交中，
  以便将当前项状态存储为撤销点。

- 添加新项时，调用 `Add( item )`。
  除非您要中止提交，否则不要删除添加的项目。

- 移除条目时，调用 `Remove( item )`。
  您不应删除已删除的项目，它将存储在撤消缓冲区中。

- 使用 `Push( "Description" )` 完成提交。
  如果您没有执行任何修改、添加或删除操作，
  则这是一个禁止操作，因此在推送之前不需要检查您是否做了任何更改。

如果您想要中止提交，可以直接销毁它，而不需要调用 `Push()`。
基础模型将不会更新。

例如：

``` cpp
// 从当前 PCB_TOOL 构造提交
BOARD_COMMIT commit( this );

BOARD_ITEM* modifiedItem = getSomeItemToModify();

// 告诉提交我们要更改项目
commit.Modify( modifiedItem );

// 更新项目
modifiedItem->Move( x, y );

// 创建新项目
PCB_SHAPE* newItem = new PCB_SHAPE;

// ... 在此设置项目

// 添加到提交
commit.Add( newItem );

// 更新模型并添加撤消点
commit.Push( "Modified one item, added another" );
```

# 教程：添加新工具

不过详细介绍 GAL 工具框架是如何在表面下实现的， 让我们看看如何向 Pcbnew
添加一个全新的工具。 我们的工具将具有以下 (相当无用) 功能：

- 一个交互式工具，允许用户选择一个点，
  从该点的项目中进行选择，然后将该项目向左移动 10 mm。

- 在此模式下，上下文菜单将有更多选项：

- "normal" 画布缩放和网格选项的使用

- 一种非交互式工具，它将在固定点添加一个固定的圆。

- 一种从 PCB_EDITOR_CONTROL 工具调用非交互式 "unfill all zones"
  工具的方法。

# 声明工具操作 {#declare-actions}

第一步是添加工具操作。 我们将实施两个名为：

- `Pcbnew.UselessTool.MoveItemLeft` - 交互式工具

- `Pcbnew.UselessTool.FixedCircle` - 非交互式工具。

名为 `pcbnew.EditorControl.zoneUnfillAll` 的 “取消填充工具” 已存在

本指南假设我们将向 Pcbnew 添加一个工具， 但其他支持 GAL
的画布的过程与此类似。

在 `pcbnew/tools/pcb_actions.h` 中，我们在 `PCB_ACTIONS`
类中添加了以下内容， 该类声明了我们的工具：

``` cpp
static TOOL_ACTION uselessMoveItemLeft;
static TOOL_ACTION uselessFixedCircle;
```

动作的定义通常发生在相关工具的 .cpp 中。 定义发生在哪里实际上并不重要
(声明足以使用操作)，只要它最终是链接的。 类似的工具应该始终一起定义。

在我们的例子中，因为我们正在制作一个新工具， 所以它将位于
`pcbnew/tools/unuble_tool.cpp` 中。
如果向现有工具添加操作，则工具字符串的前缀 (例如 `"Pcbnew.UselessTool"`)
将是在何处定义工具的强指示符。

工具定义如下所示：

``` cpp
TOOL_ACTION COMMON_ACTIONS::uselessMoveItemLeft(
        "pcbnew.UselessTool.MoveItemLeft",
        AS_GLOBAL, MD_CTRL + MD_SHIFT + int( 'L' ),
        _( "将项目左移" ), _( "选择并向左移动项目" ) );

TOOL_ACTION COMMON_ACTIONS::uselessFixedCircle(
        "pcbnew.UselessTool.FixedCircle",
        AS_GLOBAL, MD_CTRL + MD_SHIFT + int( 'C' ),
        _( "固定圆" ), _( "在固定位置添加固定大小的圆" ),
        add_circle_xpm );
```

我们为每个操作定义了热键，它们都是全局的。 这意味着您可以分别使用
`Shift+Ctrl+L` 和 `Shift-Ctrl-C` 访问各个工具。

我们为其中一个工具定义了一个图标，该图标应该出现在项目添加到的任何菜单中，
以及给定的标签和说明性工具提示。

我们现在定义了两个操作，但是它们没有连接到任何东西。
我们需要定义实现正确操作的函数。 您可以将这些添加到现有的工具中
(例如，`PCB_EDITOR_CONTROL`，它处理许多常规的 PCB
修改操作，如区域填充)，
或者您可以编写一个全新的工具来保持独立，并为您添加工具状态提供更大的空间。

我们将编写自己的工具来演示该过程。

## 添加工具类声明

添加一个新的工具类头 `pcbnew/tools/inusable_tool.h`， 包含如下类：

``` cpp
class USELESS_TOOL : public PCB_TOOL
{
public:
    USELESS_TOOL();
    ~USELESS_TOOL();

    ///> 对模型/视图更改做出反应
    void Reset( RESET_REASON aReason ) override;

    ///> 基本初始化
    bool Init() override;

    ///> 将处理程序绑定到相应的 TOOL_ACTIONs
    void setTransitions() override;

private:
    ///> “向左移动选定项” 交互式工具
    int moveLeft( const TOOL_EVENT& aEvent );

    ///> 执行左移操作的内部函数
    void moveLeftInt();

    ///> 添加固定大小的圆
    int fixedCircle( const TOOL_EVENT& aEvent );

    ///> 该工具显示的菜单模型。
    TOOL_MENU m_menu;
};
```

## 实现工具类方法

在 `pcbnew/tools/inusable_tool.cpp` 中，实现所需的方法。
在此文件中，您还可以添加免费函数助手、其他类等。

您需要将该文件添加到 `pcbnew/CMakeLists.txt` 中进行构建。

下面您将找到 useless_tool.cpp 的内容：

``` cpp
#include "useless_tool.h"

#include <class_draw_panel_gal.h>
#include <view/view_controls.h>
#include <view/view.h>
#include <tool/tool_manager.h>
#include <board_commit.h>

// 用于框架工具 ID 值
#include <pcbnew_id.h>

// 用于操作图标
#include <bitmaps.h>

// 项目工具可以作用于
#include <class_board_item.h>
#include <class_drawsegment.h>

// 访问其他 PCB 操作和工具
#include "pcb_actions.h"
#include "selection_tool.h"


/*
  * 特定于工具的操作定义
  */
TOOL_ACTION PCB_ACTIONS::uselessMoveItemLeft(
        "pcbnew.UselessTool.MoveItemLeft",
        AS_GLOBAL, MD_CTRL + MD_SHIFT + int( 'L' ),
        _( "将项目左移" ), _( "选择并向左移动项目" ) );

TOOL_ACTION PCB_ACTIONS::uselessFixedCircle(
        "pcbnew.UselessTool.FixedCircle",
        AS_GLOBAL, MD_CTRL + MD_SHIFT + int( 'C' ),
        _( "固定圆" ), _( "在固定位置添加固定大小的圆" ),
        add_circle_xpm );

/*
  * USELESS_TOOL 实现
  */

USELESS_TOOL::USELESS_TOOL() :
        PCB_TOOL( "pcbnew.UselessTool" ),
        m_menu( *this )
{
}


USELESS_TOOL::~USELESS_TOOL()
{}


void USELESS_TOOL::Reset( RESET_REASON aReason )
{
}


bool USELESS_TOOL::Init()
{
    auto& menu = m_menu.GetMenu();

    // 添加我们自己工具的操作
    menu.AddItem( PCB_ACTIONS::uselessFixedCircle );
    // 添加 PCB_EDITOR_CONTROL's 的区域取消填充所有操作
    menu.AddItem( PCB_ACTIONS::zoneUnfillAll );

    // 添加标准缩放和栅格工具动作
    m_menu.AddStandardSubMenus( *getEditFrame<PCB_BASE_FRAME>() );

    return true;
}

void USELESS_TOOL::moveLeftInt()
{
    // 我们将调用选择工具上的操作来获取当前选择。
    // 选择工具将处理项目的歧义消除
    PCB_SELECTION_TOOL* selectionTool = m_toolMgr->GetTool<PCB_SELECTION_TOOL>();
    assert( selectionTool );

    // 调用操作
    m_toolMgr->RunAction( PCB_ACTIONS::selectionClear, true );
    m_toolMgr->RunAction( PCB_ACTIONS::selectionCursor, true );
    selectionTool->SanitizeSelection();

    const SELECTION& selection = selectionTool->GetSelection();

    // 未选择任何内容，返回到事件循环
    if( selection.Empty() )
        return;

    BOARD_COMMIT commit( this );

    // 迭代 BOARD_ITEM* 容器，移动每个项目
    for( auto item : selection )
    {
        commit.Modify( item );
        item->Move( wxPoint( -5 * IU_PER_MM, 0 ) );
    }

    // push commit - 如果选择为空，则这是无操作
    commit.Push( "Move left" );
}

int USELESS_TOOL::moveLeft( const TOOL_EVENT& aEvent )
{
    auto& frame = *getEditFrame<PCB_EDIT_FRAME>();

    // 设置工具提示和光标 (实际上看起来像十字准线)
    frame.SetToolID( ID_NO_TOOL_SELECTED,
            wxCURSOR_PENCIL, _( "选择要向左移动的项目" ) );

    getViewControls()->ShowCursor( true );

    Activate();

    // 只要工具处于活动状态，就处理工具事件
    while( OPT_TOOL_EVENT evt = Wait() )
    {
        if( evt->IsCancel() || evt->IsActivate() )
        {
            // 交互式工具结束
            break;
        }
        else if( evt->IsClick( BUT_RIGHT ) )
        {
            m_menu.ShowContextMenu();
        }
        else if( evt->IsClick( BUT_LEFT ) )
        {
            // 调用主操作逻辑
            moveLeftInt();

            // 保持显示编辑光标
            getViewControls()->ShowCursor( true );
        }
    }

    // 将 PCB 框架重置为我们得到的原样
    frame.SetToolID( ID_NO_TOOL_SELECTED, wxCURSOR_DEFAULT, wxEmptyString );
    getViewControls()->ShowCursor( false );

    // 退出操作
    return 0;
}


int USELESS_TOOL::fixedCircle( const TOOL_EVENT& aEvent )
{
    // 要添加的新圆 (理想情况下使用智能指针)
    PCB_SHAPE* circle = new PCB_SHAPE;

    // 设置圆形属性
    circle->SetShape( S_CIRCLE );
    circle->SetWidth( 5 * IU_PER_MM );
    circle->SetStart( wxPoint( 50 * IU_PER_MM, 50 * IU_PER_MM ) );
    circle->SetEnd( wxPoint( 80 * IU_PER_MM, 80 * IU_PER_MM ) );
    circle->SetLayer(  LAYER_ID::F_SilkS );

    // 把圆形交到 BOARD 上
    BOARD_COMMIT commit( this );
    commit.Add( circle );
    commit.Push( _( "画一个圆形" ) );

    return 0;
}


void USELESS_TOOL::setTransitions()
{
    Go( &USELESS_TOOL::fixedCircle, PCB_ACTIONS::uselessFixedCircle.MakeEvent() );
    Go( &USELESS_TOOL::moveLeft,    PCB_ACTIONS::uselessMoveItemLeft.MakeEvent() );
}
```

## 注册该工具

最后一步是在工具管理器中注册工具。

对于任何支持该工具的 `EDA_DRAW_FRAME`， 这都是在框架的 `setupTools()`
函数中完成的。

## 编译并运行

完成所有操作后，您应该已经修改了以下文件：

- `pcbnew/tools/common_actions.h` - 操作声明

- `pcbnew/tools/useless_tool.h` - 工具头文件

- `pcbnew/tools/useless_tool.cpp` - 操作定义和工具实现

- `pcbnew/tools/tools_common.cpp` - 工具的注册

- `pcbnew/CMakeLists.txt` - 用于编译建新的 .cpp 文件

当您运行 Pcbnew 时，您应该可以按 `Shift+Ctrl+L` 进入 “向左移动项目”
工具-光标将变为十字准线， 并在右下角出现 “选择要向左移动的项目”。

当你点击鼠标右键时，你会看到一个菜单， 其中包含一个用于我们的
“创建固定圆” 工具的条目和 一个用于我们添加到菜单中的现有的
“取消填充所有区域” 工具的条目。 您也可以使用 `Shift+Ctrl+C`
进入固定圆操作。

恭喜您，您刚刚创建了您的第一个 KiCad 工具！
