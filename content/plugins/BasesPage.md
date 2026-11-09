---
date: 2026-09-20
title: Bases 页面 (BasesPage)
description: 将 Obsidian Bases 文件渲染为数据库风格的视图。
tags:
  - plugin/pageType
  - plugin/component
image:
new-in-v5: true
repository: "[quartz-community/bases-page](https://github.com/quartz-community/bases-page)"
enabled: true
required: false
---

该插件为 Quartz 提供对 [Obsidian Bases](https://obsidian.md/changelog/2025-04-15-desktop-v1.8.0/)（`.base` 文件）的支持。它从你的库中读取 `.base` 文件，根据查询定义解析匹配的笔记，并将它们渲染为支持表格、列表、卡片和地图的交互式数据库风格视图。它使用 `default` [[layout#页面框架|页面框架]]（带侧边栏的三栏布局）。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 特性

- **表格视图**：可排序的列，并自动进行类型渲染（字符串、数字、布尔值、数组、链接）。
- **列表视图**：紧凑列表，每个条目带有元数据标签。
- **卡片视图**：卡片布局，可选支持图片属性。
- **地图视图**：为未来基于地图的可视化预留的占位。
- **多视图**：单个 `.base` 文件可以定义多个视图，以可切换的标签页形式显示。
- **过滤器**：使用 `and`/`or`/`not` 运算符的递归过滤器树。
- **公式**：通过公式表达式计算属性。
- **汇总**：列级聚合（求和、平均值、最小值、最大值、中位数等）。
- **属性配置**：为属性自定义显示名称。
- **链接渲染**：单元格值中的维基链接和 Markdown 链接会渲染为可点击的链接。

## 与 `unlisted` 页面的交互

`BasesPage` 遵循由 [[UnlistedPages]] 和 [[EncryptedPages]] 写入的 `file.data.unlisted` 约定。标记为 `unlisted: true` 的页面（或带有 `stealth: true` 的加密页面）会被排除在所有已渲染的 base 视图之外——表格、列表、看板、卡片、画廊以及任何自定义视图——无论 base 的过滤器表达式是否会匹配到它们。未列出的页面也无法通过 `.asFile()` 从可见页面上的公式中解引用。

> [!note]
> Base 视图是构建时烘焙的**服务端渲染** HTML。当访问者解密加密页面后，它们不会在客户端更新。关系图谱、资源管理器和搜索都会从修补后的内存内容索引中重新水合，并在本次浏览器会话的剩余时间内显示新解锁的页面——base 视图则不会，因为它们是在构建时物化的，那时未列出的页面已被排除。成功解密可显示加密页面的访问者会看到它出现在关系图谱、资源管理器和搜索中，但**不会**出现在任何 base 视图中，直到站点在包含该页面的情况下重新构建。这与反向链接、最近笔记、文件夹列表和标签列表所受的结构性限制相同。

## 配置

该插件接受以下配置选项：

- `defaultViewType`：当 `.base` 文件中未指定视图类型时的默认视图类型。默认为 `"table"`。
- `linkResolution`：如何在视图渲染器中解析内部链接。应与 [[CrawlLinks]] 插件的 `markdownLinkResolution` 设置一致。可以是 `"absolute"`、`"relative"` 或 `"shortest"`。默认为 `"shortest"`。
- `customViews`：自定义视图渲染器的映射。键为视图类型名称。它们会覆盖同一类型的内置渲染器，或添加新的视图类型。需要 TS 覆盖。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/bases-page
  enabled: true
```

对于自定义视图渲染器，请在 `quartz.ts` 中使用 TS 覆盖：

```ts title="quartz.ts (override)"
import * as ExternalPlugin from "./.quartz/plugins"

// Must be placed before loadQuartzConfig()
ExternalPlugin.BasesPage({
  defaultViewType: "table",
  customViews: {
    myView: ({ entries, view, basesData, total, locale }) => {
      // return JSX
    },
  },
})
```

## API

- 类别：页面类型、组件
- 函数名：`ExternalPlugin.BasesPage()`。
- 来源：[`quartz-community/bases-page`](https://github.com/quartz-community/bases-page)
- 安装：`npx quartz plugin add github:quartz-community/bases-page`
