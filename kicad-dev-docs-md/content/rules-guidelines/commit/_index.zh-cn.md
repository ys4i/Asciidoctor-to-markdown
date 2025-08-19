title: 提交规范 weight: 2 summary: 关于如何格式化 git 提交信息的指引 ---

# 提交信息格式规范

提交消息应以简短的主题行开头。 请尝试将其限制为不超过 72 个字符。
邮件正文与主题行之间应用空行分隔，并以 72 个字符换行。
提交信息的内容应该解释这次提交的作用和原因。
不要描述改动是如何工作的，因为代码本身已经足够说明问题了。

# 将提交和问题联系起来

如果提交修复了 [issue
跟踪器](https://gitlab.com/kicad/code/kicad/issues) 中报告的问题，
请在提交消息中添加一行指示修复的问题编号。 在这种情况下，GitLab
将自动关闭该问题，并添加指向您在该问题中提交的链接。

比如，下面这一行就会自动关闭问题 \#1234567：

    Fixes https://gitlab.com/kicad/code/kicad/issues/1234567

有一个 [alias](#commit_fixes_alias) 可以简化这一步骤。 您可以在 [GitLab
文档](https://docs.gitlab.com/ee/user/project/issues/managing_issues.html#closing-issues-automatically)
中阅读有关自动问题关闭的更多信息。

# 变更记录标签

为了帮助记录下面的代码改动，应该要包含一条给用户提示改动的变更记录标签。有三种变更
记录标签：

- `ADDED` 来表示新功能

- `CHANGED` 来表示对现有功能的改动

- `REMOVED` 来通知现有功能的删除

如果提交没有改变用户和软件的交互，比如代码重构，问题缺陷修复等，就没有必要添加变更
记录标签。变更记录标签的主要目的是生成发布说明来向文档维护者提示改动。一定要记得什
么情况下需要添加变更记录标签。

## 让文档开发者意识到改动

当推送有变更记录标签的提交时，提交者应该到
[文档仓库](https://gitlab.com/kicad/services/kicad-doc/issues)
创建一个新问题，用于提示文档维护者。这个问题里包含一个关于这次提交的链接。

## 提取变更记录

得益于变更记录标签，我们很容易通过下面的 git 命令提取出变更记录：

``` sh
    git log -E --grep="ADD[ED]?:|REMOVE[D]?:|CHANGE[D]?:" --since="1 Jan 2017"
    git log -E --grep="ADD[ED]?:|REMOVE[D]?:|CHANGE[D]?:" <commit hash>
```

KiCad 提供了一个 [alias](#commit_changelog_alias)
来简化变更记录提取命令。

## 例子

下面是一个正确的提交信息格式的例子：

        Eeschema: Adding line styling options

        ADDED: Add support in Eeschema for changing the default line style,
        width and color on a case-by-case basis.

        CHANGED: "Wire" lines now optionally include data on the line style,
        width and color if they differ from the default.

        Fixes https://gitlab.com/kicad/code/kicad/issues/594059
        Fixes https://gitlab.com/kicad/code/kicad/issues/1405026

# git 别名文件

这个 `helpers/git/fixes_alias` 文件包含有一些很有用的 git 别名。可以通过
在代码仓库运行下面的命令来安装它：

``` sh
    git config --add include.path $(pwd)/helpers/git/fixes_alias
```

## 'fixes' 别名

一旦别名配置文件安装好后，就可以用它来修改最近一次提交的信息，使之包含问题报告的
链接：

    git fixes 1234567

这个例子中，这条命令会在最后一次提交的信息里添加下面这一行内容：

    Fixes https://gitlab.com/kicad/code/kicad/issues/1234567

## 'changelog' 别名

别名配置文件安装好后，你就可以通过下面的别名来提取变更日志：

``` sh
    git changelog --since="1 Jan 2017"
    git changelog <commit hash>
```
