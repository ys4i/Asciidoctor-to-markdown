title: 贡献 weight: 1 pre: "\<i class='fas fa-chevron-right'\>\</i\> "
aliases: - /zh-cn/contribute/ ---

# 为 KiCad (应用程序) 开发做出贡献

KiCad 一直是工程师在有限的时间内进行开发的社区驱动的努力。
我们欢迎任何人贡献错误修复、改进和新功能。

## 开发者邮件列表

做任何事之前要做的第一件事就是加入。 [KiCad
开发人员邮件列表](https://launchpad.net/~kicad-developers)
在这里，您可以提出广泛的问题，并提出您的想法或计划，如果它超出了错误修复。

## 获取代码

KiCad 使用
[Git](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git)
用于源代码控制。

如果您是 git 新手，强烈建议您。 查找并遵循在线提供的许多教程，如。
[这一个](http://learngitbranching.js.org/) 和/或阅读 git
[文档](https://git-scm.com/doc)。

我们还致力于使用 GitLab 作为我们 Git 仓库的主要托管平台。

所有仓库都可以在 [这里](https://gitlab.com/kicad/) 找到。

主应用程序的仓库可以在 [这里](https://gitlab.com/kicad/code/kicad/) 找到

## 编译代码

按照您的平台对应的 [文档](../build/) 中的说明设置工作构建环境，
并从源代码成功构建 KiCad。

## 文档代码

要熟悉代码库，您可以阅读 Doxygen 生成的文档。
提交新代码时，请记住更新文档说明。 您可以运行 `make doxygen-docs`
在本地生成文档。

Jenkins 服务器上也提供了相同文档的最新版本， 请参阅 [C++ API 的 KiCad
开发人员文档](http://docs.kicad.org/doxygen/namespaces.html)。

您还可以在 Doxygen 文档中找到各种开发人员备注， 请参见
[这个相关页面](http://docs.kicad.org/doxygen/pages.html)。

## 提交之前

进行更改后，请确保您的代码符合以下 [KiCad
编码样式策略](../rules-guidelines/code-style/)。

您的提交消息应遵循说明的规则中 [KiCad
提交消息格式策略](../rules-guidelines/commit/)

如果您想在用户界面上工作，您会发现阅读
[用户界面指南](../rules-guidelines/ui/)。

让所有开发人员都能读懂代码库对我们来说很重要。
除非遵守这些政策中规定的规则，否则不会接受您的补丁程序。

编程是一个迭代的过程，如果您必须回去更改您的补丁，请不要担心，我们都是这样做的。

## 提交代码

所有补丁都需要在 GitLab 上以 **合并请求** 的形式提交。
有关如何创建合并请求的信息，请参阅此
[如何为新用户创建合并请求](https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html)。

**注意：KiCad 有一个 GitHub 镜像，但所有拉取请求都会被忽略，我们只接受
GitLab 上的更改**

## 修复所有 CI 问题

GitLab 仓库启用了持续集成
(CI)。这意味着它会自动对传入的提交运行处理步骤，以执行从确保代码可以编译到编码样式策略等几个任务。

您可以在合并请求页面上找到配置项作业的状态并查看其输出。如果您需要帮助，您可以在合并请求中要求开发人员评论如何解决问题。

# 初学者 / 初学者补丁

大体上没有什么想法，但想帮忙吗？

你可能需要调查一下 [标签为 ‘Starter’
的问题](https://gitlab.com/kicad/code/kicad/issues?scope=all&utf8=%E2%9C%93&state=opened&label_name[]=starter)。

例如，如果您是 macOS 用户，您可能需要检查以下内容： [标记为 'macOS'
的问题](https://gitlab.com/kicad/code/kicad/issues?scope=all&utf8=%E2%9C%93&state=opened&label_name[]=macos)。

# 互联网中继聊天(*IRC*)

欢迎在 [\#kicad@liberachat](ircs://irc.libera.chat:6697/#kicad). 上加入
irc 频道。
那里有一群不错的人在闲聊，所以如果你有任何问题，不知道去哪里问，你应该试着在这里问。
在各种时区都有各种各样的人，既有开发 KiCad 的人，也有普通的热心用户。
