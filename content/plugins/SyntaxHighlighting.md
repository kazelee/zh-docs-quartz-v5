---
date: 2026-09-20
title: 语法高亮 (SyntaxHighlighting)
description: 为代码块提供语法高亮。
tags:
  - plugin/transformer
image: https://images.unsplash.com/photo-1580569214296-5cf2bffc5ccd
repository: "[quartz-community/syntax-highlighting](https://github.com/quartz-community/syntax-highlighting)"
enabled: true
required: false
---

本插件用于为 Quartz 中的代码块添加语法高亮。更多信息请参阅 [[syntax highlighting]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

本插件接受以下配置选项：

- `theme`：分别指定 [Shikiji 内置主题](https://shikiji.netlify.app/themes)之一的 id。一个用于浅色模式，一个用于深色模式。默认为 `theme: { light: "github-light", dark: "github-dark" }`。
- `keepBackground`：若设为 `true`，将使用 Shikiji 主题的背景。若为 `false`（默认），则改为使用 Quartz 主题的背景色。
- `clipboard`：是否为代码块添加复制到剪贴板的按钮。默认为 `true`。
- `tokenClassification`：是否为代码 token 添加语义化的 token 分类 CSS 类。默认为 `true`。

此外，你还可以在 `quartz/styles/syntax.scss` 文件中进一步覆盖颜色。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.SyntaxHighlighting()`。
- 来源：[`quartz-community/syntax-highlighting`](https://github.com/quartz-community/syntax-highlighting)
- 安装：`npx quartz plugin add github:quartz-community/syntax-highlighting`
