---
date: 2026-09-20
title: 文章标题 (ArticleTitle)
description: 将文章标题渲染为 h1 标题。
tags:
  - plugin/component
image:
repository: "[quartz-community/article-title](https://github.com/quartz-community/article-title)"
enabled: true
required: false
---

该插件将页面 frontmatter 中的文章标题渲染为页面内容顶部的 `<h1>` 标题。它读取 frontmatter 中的 `title` 字段（如果未设置标题，则回退到文件名）。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件没有配置选项。

## API

- 类别：组件
- 函数名：`ExternalPlugin.ArticleTitle()`。
- 来源：[`quartz-community/article-title`](https://github.com/quartz-community/article-title)
- 安装：`npx quartz plugin add github:quartz-community/article-title`
