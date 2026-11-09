---
date: 2026-09-20
title: "Roam Research 兼容性"
tags:
  - feature/transformer
---

[Roam Research](https://roamresearch.com) 是一款笔记工具，它以独特且相互连接的方式组织你的知识图谱。

Quartz 支持通过 [[RoamFlavoredMarkdown]] 插件将 Roam Research 的特殊 Markdown 语法（例如 `{{[[components]]}}` 和其他格式）转换为常规 Markdown。

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/roam
    enabled: true
    order: 25 # 必须位于 obsidian-flavored-markdown 之前
  - source: github:quartz-community/obsidian-flavored-markdown
    enabled: true
    order: 30
```

对于 TS 覆盖方式，请将覆盖放在 `quartz.ts` 中 `loadQuartzConfig()` 之前：

```ts title="quartz.ts (override)"
import * as ExternalPlugin from "./.quartz/plugins"

ExternalPlugin.RoamFlavoredMarkdown()
```

> [!warning]
> 在 YAML 中，插件执行顺序由 `order` 字段控制。请确保 `roam` 的 `order` 值小于 `obsidian-flavored-markdown`，以便它先运行。

## 自定义

此功能由 [[RoamFlavoredMarkdown]] 插件提供。有关自定义选项，请参阅该插件页面。
