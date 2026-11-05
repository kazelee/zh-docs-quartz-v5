---
title: 全文搜索
tags:
  - component
---

Quartz 中的全文搜索由 [Flexsearch](https://github.com/nextapps-de/flexsearch) 提供支持。它足够快，即使 Quartz 站点大到五十万词，也能在 10 毫秒以内返回搜索结果。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

你可以通过点击搜索栏，或按 `⌘`/`ctrl` + `K` 来打开它。每次查询会显示前 5 条搜索结果。匹配的子词会被高亮，并摘录最相关的 30 个词。点击搜索结果会跳转到该页面。

要按标签搜索内容，你可以按 `⌘`/`ctrl` + `shift` + `K`，或以 `#` 开头输入查询（例如 `#components`）。

该组件也支持键盘操作：Tab 和 Shift+Tab 会在搜索结果中向前和向后循环，Enter 会跳转到高亮的结果（默认是第一条结果）。你也可以使用 `ArrowUp` 和 `ArrowDown` 在搜索结果中导航。

> [!info]
> 搜索要求 [[configuration]] 中存在 `ContentIndex` 输出器插件。

### 索引行为

默认情况下，它会为站点上的每个页面建立索引，且**移除 Markdown 语法**。这意味着例如链接 URL 不会被索引。

它能正确地切分中文、韩文和日文字符，并为标题、内容和标签分别构建索引，其中标题匹配的权重高于内容匹配。

## 自定义

- 移除搜索：从 `quartz.config.yaml` 中移除 `search` 条目，或设置 `enabled: false`。
- 安装：`npx quartz plugin add github:quartz-community/search`
- 源码：[`quartz-community/search`](https://github.com/quartz-community/search)
