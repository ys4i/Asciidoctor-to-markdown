title: 稳定的发布政策 weight: 4 summary: 稳定版政策为 KiCad
项目提供了稳定版的指导方针。 ---

# 1 简介

本文件的目的是为 KiCad 项目提供创建版本的指南。

<div class="note">

本文件适用于 6.0.0 版本之后的所有稳定版本。

</div>

<div class="warning">

未经项目负责人同意，请勿修改本文件。对本文件的所有修改都需要批准。

</div>

## 1.1 版本管理计划

KiCad 使用典型的 \[major\].\[minor\].\[bug fix\] 版本划分方案。
每个新版本都是前一版本的简单增量。
新的主要版本将把次要版本和错误修复版本重置为 0。

## 1.2 限制条件

在一个主要的发布周期内，不允许对电路板、原理图、封装库、符号库或工程文件格式进行修改，除非是修复已知的错误。

小版本和错误修复版本只对当前的稳定大版本有效。一旦新的稳定大版本发布，之前的稳定版本就被视为不再支持（弃用）的分支。

# 2 主要发布政策

主要的发布将在每年的 1 月 31 日之前进行。这与每年的
[FOSDEM](https://fosdem.org/) 会议相一致。下表列出了发布时间表的日期。

<table>
<caption>主要发布时间表</caption>
<colgroup>
<col style="width: 16%" />
<col style="width: 83%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">日期</th>
<th style="text-align: left;">里程碑</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>2 月 1 日</p></td>
<td style="text-align: left;"><p>新功能合并窗口打开。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>9 月 30 日</p></td>
<td style="text-align: left;"><p>新功能合并窗口关闭。</p>
<p>仅修复错误的时期开始。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>12 月 1 日</p></td>
<td style="text-align: left;"><p>字符串冻结开始。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>12 月 15 日</p></td>
<td style="text-align: left;"><p>候选版本 RC1 被标记。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>1 月 14 日</p></td>
<td style="text-align: left;"><p>库、文件和，翻译库的冻结。</p>
<p>错误修复冻结。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>1 月 15 日</p></td>
<td
style="text-align: left;"><p>源代码库被标记为下一个主要版本并被分支。</p>
<p>开始打包并上传到网站。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>1 月 31 日</p></td>
<td style="text-align: left;"><p>新版本发布公告。</p></td>
</tr>
</tbody>
</table>

主要发布时间表

<div class="warning">

在合并窗口关闭前没有准备好的功能将不得不等到下一个合并窗口。**没有例外**。

</div>

<div class="note">

只有源代码库的分支是强制性的。
如果开发团队认为有必要，所有其他仓库都会被分支。

</div>

# 3 次要版本政策

只要满足以下条件，KiCad 当前开发分支的功能就可以回溯到当前稳定分支:

- 最后一次特性提交是在合并到当前稳定分支之前至少 60 天推送到开发分支的。

- 最后一次针对该特性的已知问题是在合并到当前稳定版分支之前至少 30
  天发生的。

- 摘选到当前稳定分支的过程中，可以不拉动任何其他符合上述文件格式变化标准的依赖代码。

一个新的次要版本将包括当前错误修复版本中所包含的所有错误修复，因此对于新的次要版本发布，错误修复版本将被重置为
0。

<div class="note">

开发分支的反向移植只能应用于当前的稳定分支。
不允许向更早的稳定版本进行反向移植。

</div>

# 4 错误修复发布政策

错误修复的发布是由主导开发团队决定的。

错误修复发布将由项目经理在发布前 30
天宣布，以便有时间完成所有必要的发布行动。

<div class="note">

除了拼写和语法错误，错误修复不能包含可翻译的字符串变化。

</div>

<div class="note">

错误修复只能应用于当前的稳定版。

</div>
