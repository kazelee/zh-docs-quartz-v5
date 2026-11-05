---
title: Canvas 页面 (CanvasPage)
description: 将 JSON Canvas 文件渲染为可交互、可平移的页面。
tags:
  - plugin/pageType
image: "#7852ee"
new-in-v5: true
repository: "[quartz-community/canvas-page](https://github.com/quartz-community/canvas-page)"
enabled: true
required: false
---

该插件是一个页面类型插件，用于将 [JSON Canvas](https://jsoncanvas.org)（`.canvas`）文件渲染为可交互、可平移和可缩放的 Canvas 页面。它使用自定义的 `"canvas"` [[layout#Page Frames|页面框架]]，提供全屏、常驻的 Canvas 体验，并带有可切换的左侧边栏用于导航。它支持完整的 [JSON Canvas 1.0 规范](https://jsoncanvas.org/spec/1.0/)，包括可渲染 Markdown 的文本节点、链接到库中其他页面的文件节点、用于外部 URL 的链接节点，以及用于视觉组织的分组节点。节点之间的连线渲染为 SVG 路径，可选带标签、箭头标记和颜色。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件接受以下配置选项：

- `enableInteraction`：是否在 Canvas 上启用平移和缩放交互。默认值：`true{:ts}`。
- `initialZoom`：Canvas 首次显示时的初始缩放级别。默认值：`1{:ts}`。
- `minZoom`：缩小时允许的最小缩放级别。默认值：`0.1{:ts}`。
- `maxZoom`：放大时允许的最大缩放级别。默认值：`5{:ts}`。

### Canvas 框架

canvas-page 插件通过 [[layout#Page Frames|框架注册表]] 提供自己的 `"canvas"` 页面框架。该框架：

- 默认以**全屏模式**渲染 Canvas（100vw × 100vh），为 Canvas 提供最大的屏幕空间——顺应 JSON Canvas 的「无尽画布」概念。
- 提供**可切换的左侧边栏**，从左边缘滑入。这是唯一可用的布局槽位——它渲染与内容页面 `left` 侧边栏相同的组件（例如资源管理器、搜索、页面标题）。
- 侧边栏切换按钮（汉堡/关闭图标）位于左上角。
- Canvas 控件（放大、缩小、重置）位于右侧。
- 在移动端，侧边栏会覆盖在 Canvas 之上，而不是将其推到一旁。

如有需要，用户可以通过 `quartz.config.yaml` 覆盖该框架：

```yaml title="quartz.config.yaml"
layout:
  byPageType:
    canvas:
      template: default # Use standard three-column layout instead
```

### 特性

- **文本节点**：通过 [GFM](https://github.github.com/gfm/) 支持渲染 Markdown 内容，包括标题、粗体、斜体、删除线、列表、链接和代码块。
- **文件节点**：链接到库中的其他页面。支持悬停时的浮层预览。
- **链接节点**：引用外部 URL。
- **分组节点**：可视化的分组容器，可选带标签和背景颜色。
- **连线**：节点之间的 SVG 连接，可选带标签、箭头标记和颜色。支持全部四个方向（上、右、下、左），以及预设颜色（1–6）和自定义十六进制颜色。
- **可切换的侧边栏**：左上角的汉堡按钮可切换用于导航的左侧边栏。按 `Escape` 或点击关闭按钮即可收起。
- **预设颜色**：六种预设颜色（红、橙、黄、绿、青、紫），以及用于节点和连线的自定义十六进制颜色（`#RRGGBB`）。

## API

- 类别：页面类型
- 函数名：`ExternalPlugin.CanvasPage()`。
- 来源：[`quartz-community/canvas-page`](https://github.com/quartz-community/canvas-page)
- 安装：`npx quartz plugin add github:quartz-community/canvas-page`
