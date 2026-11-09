---
date: 2026-09-20
title: 资源管理器 (Explorer)
description: 文件树资源管理器侧边栏。
tags:
  - plugin/component
image:
repository: "[quartz-community/explorer](https://github.com/quartz-community/explorer)"
enabled: true
required: false
---

文件树资源管理器侧边栏。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

详细用法信息请参阅 [[plugins/Explorer]]。

## 配置

该插件接受以下配置选项：

**YAML 选项**（位于 `quartz.config.yaml` 中）：

- `title`：资源管理器的标题。默认为 `Explorer`。
- `folderClickBehavior`：点击文件夹时的行为。可以是 `"link"`（导航）或 `"collapse"`（切换展开状态）。默认为 `link`。
- `folderDefaultState`：文件夹的默认状态。可以是 `"collapsed"` 或 `"open"`。默认为 `collapsed`。
- `useSavedState`：是否使用本地存储保存资源管理器的状态。默认为 `true`。

**TS 覆盖选项**（位于 `quartz.ts` 中，用于无法在 YAML 中表达的回调函数）：

- `sortFn`：用于排序文件和文件夹的自定义排序函数。
- `filterFn`：用于排除特定节点的自定义过滤函数。
- `mapFn`：用于转换节点属性（例如显示名称）的自定义映射函数。
- `order`：控制操作顺序的数组。默认为 `["filter", "map", "sort"]`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/explorer
  enabled: true
  options:
    title: Explorer
    folderClickBehavior: link
    folderDefaultState: collapsed
    useSavedState: true
```

### TS 覆盖示例

```ts title="quartz.ts"
import * as ExternalPlugin from "./.quartz/plugins"

// 必须放在 loadQuartzConfig() 之前
ExternalPlugin.Explorer({
  mapFn: (node) => {
    node.displayName = node.displayName.toUpperCase()
    return node
  },
})
```

更多示例请参阅 [[features/explorer#高级自定义]]。

## API

- 类别：组件
- 函数名：`ExternalPlugin.Explorer()`。
- 来源：[`quartz-community/explorer`](https://github.com/quartz-community/explorer)
- 安装：`npx quartz plugin add github:quartz-community/explorer`
