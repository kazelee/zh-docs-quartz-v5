---
date: 2026-09-20
title: 目录 (TableOfContents)
description: 根据标题生成并渲染目录。
tags:
  - plugin/transformer
  - plugin/component
image: https://images.unsplash.com/photo-1768527338896-3765921e992d
repository: "[quartz-community/table-of-contents](https://github.com/quartz-community/table-of-contents)"
enabled: true
required: false
---

本插件为 Markdown 文档生成目录（TOC）。更多信息请参阅 [[table of contents]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

本插件接受以下配置选项：

- `maxDepth`：限制包含在目录中的标题深度，范围为 `1`（仅顶级标题）到 `6`（所有标题层级）。默认为 `3`。
- `minEntries`：显示目录所需的最少标题条目数。默认为 `1`。
- `showByDefault`：若为 `true`（默认），目录默认显示。可被 frontmatter 设置覆盖。
- `collapseByDefault`：若为 `true`，目录将以折叠状态开始。默认为 `false`。
- `layout`：目录组件的视觉布局。可以为 `"modern"` 或 `"legacy"`。默认为 `"modern"`。

> [!warning]
> 本插件需要 `quartz.config.yaml` 中的 `Plugin.TableOfContents` 组件来确定目录的显示位置。缺少该组件则不会显示任何内容。它们应始终一起添加或移除。

## API

- 类别：转换器、组件
- 函数名：`ExternalPlugin.TableOfContentsTransformer()`。
- 来源：[`quartz-community/table-of-contents`](https://github.com/quartz-community/table-of-contents)
- 安装：`npx quartz plugin add github:quartz-community/table-of-contents`
