---
title: Roam 风格 Markdown (RoamFlavoredMarkdown)
description: 兼容 Roam Research 导出格式。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/roam](https://github.com/quartz-community/roam)"
enabled: false
required: false
---

本插件提供对 [Roam Research](https://roamresearch.com) 的兼容支持。更多信息请参阅 [[Roam Research compatibility]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

本插件接受以下配置选项：

- `orComponent`：若为 `true`（默认），将 Roam `{{ or:ONE|TWO|THREE }}` 短代码转换为 HTML 下拉选项。
- `TODOComponent`：若为 `true`（默认），将 Roam `{{[[TODO]]}}` 短代码转换为 HTML 复选框。
- `DONEComponent`：若为 `true`（默认），将 Roam `{{[[DONE]]}}` 短代码转换为已勾选的 HTML 复选框。
- `videoComponent`：若为 `true`（默认），将 Roam `{{[[video]]:URL}}` 短代码转换为嵌入的 HTML 视频。
- `audioComponent`：若为 `true`（默认），将 Roam `{{[[audio]]:URL}}` 短代码转换为嵌入的 HTML 音频。
- `pdfComponent`：若为 `true`（默认），将 Roam `{{[[pdf]]:URL}}` 短代码转换为嵌入的 HTML PDF 查看器。
- `blockquoteComponent`：若为 `true`（默认），将 Roam `{{[[>]]}}` 短代码转换为 Quartz 引用块。
- `tableComponent`：若为 `true`（默认），将 Roam 表格语法转换为 HTML 表格。
- `attributeComponent`：若为 `true`（默认），将 Roam 属性语法转换为渲染后的属性。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.RoamFlavoredMarkdown()`。
- 来源：[`quartz-community/roam`](https://github.com/quartz-community/roam)
- 安装：`npx quartz plugin add github:quartz-community/roam`
