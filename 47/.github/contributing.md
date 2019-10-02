# Harbinger 贡献指南

看起来你是想为 Harbinger 的最终完成贡献自己的一份力量，所以才点到了这里。本指南简要介绍了如何向 Harbinger 提交新的 Issue Ticket 和/或发起拉取请求（Pull Request）。

## 如果你发现了问题，但自己没有能力解决……

我们推荐你不要贸然尝试自己解决，而是提交 Issue Ticket。提交 Issue Ticket 时会有现成的模板，直接按模板填写你发现的问题即可。  
虽然如此，但在提交新的 Issue Ticket 之前，请检查以下几点：

  * 是否已经有其他人提交过一模一样的 Issue Ticket 了？
    * 对于重复的 Issue Ticket，我们只会保留最早提交的一份，并关闭剩余的 Issue Ticket。
  * 这里不是 Java 教程。
    * 所有有关基础 Java 知识的 Issue Ticket 通常都会被直接关闭。
    * 若你提交的 Issue Ticket 是关于 Harbinger 自身的某段 Java 代码的，我们会视具体情况决定。
  * 请务必使用我们提供的模板。

## 如果你发现了问题，同时自己有能力解决……

非常好，此时你多了一选项：Pull Request。PR 的流程相对比较复杂，因为本项目需要遵守的规则相对较多。下面是需要注意的点：

  * 请阅读我们的 Code of Conduct。
  * 我们使用一套简化后的 Angular 的 commit message 模板。
    * 所有的 commit message 均需要匹配这个正则表达式：`(feat|fix|refactor|style)\(<scope>\): .+`。
    * 关于 `feat`、`fix`、`refactor`、`style`：
      * `feat` 代表引入了全新内容的 commit
      * `fix` 代表修正了错误内容的 commit
      * `refactor` 代表一般的内容订正（语法错误、简化表达、解释细节等等）
      * `style` 代表一般的 Markdown 排版修正
    * `<scope>` 可以是：
      * `Ch. 01` 到 `Ch. 40` 中的任意一个章节
      * `preface`（序言）
      * `aftrword`（后记）
      * `gitbook`（GitBook 构建相关）
      * 以逗号 `,` 连接的多个上述可能的值
    * 对于存在不使用这套模板的 commit 的 PR，我们会使用 Squash Merge。
  * 我们会提供一套 Pull Request 模板，请按照模板填写有关内容。
