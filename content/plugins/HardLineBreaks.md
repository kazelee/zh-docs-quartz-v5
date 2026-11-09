---
date: 2026-09-20
title: 硬换行 (HardLineBreaks)
description: 将单个换行视为硬换行。
tags:
  - plugin/transformer
image: "#ff8000"
repository: "[quartz-community/hard-line-breaks](https://github.com/quartz-community/hard-line-breaks)"
enabled: false
required: false
---

本插件会自动将 Markdown 文本中的单个换行转换为 HTML 输出中的硬换行。由于这并不符合实际 Markdown 的语义，因此该插件默认未启用，但如果你希望与 [[Obsidian compatibility|Obsidian]] 保持一致，可以启用它。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件没有配置选项。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.HardLineBreaks()`。
- 来源：[`quartz-community/hard-line-breaks`](https://github.com/quartz-community/hard-line-breaks)
- 安装：`npx quartz plugin add github:quartz-community/hard-line-breaks`
