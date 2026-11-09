---
date: 2026-09-20
title: "资源管理器"
tags:
  - component
---

Quartz 提供了一个资源管理器，让你可以浏览站点上的所有文件和文件夹。它支持嵌套文件夹，并且高度可自定义。

> [!info]
> 资源管理器现在是一个社区插件。这演示了外部插件如何在扩展 Quartz 功能的同时，充当面向插件开发者的参考实现。

## 安装

资源管理器以社区插件的形式在 GitHub 上提供：

```bash
npm install github:quartz-community/explorer --legacy-peer-deps
```

然后把它添加到你的 `quartz.config.yaml`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
    layout:
      position: left
      priority: 50
```

## 功能

默认情况下，它会显示页面上的所有文件夹和文件。要把资源管理器显示在其他位置，你可以编辑 [[layout]]。

文件夹的显示名称由 `folder/index.md` 中的 `title` frontmatter 字段决定（详见 [[authoring content | 撰写内容]]）。如果该文件不存在或不包含 frontmatter，则会改用本地文件夹名。

> [!info]
> 资源管理器默认使用本地存储来保存其状态。这样做是为了确保在切换到不同页面时获得流畅的体验。
>
> 要清除/删除本地存储中的资源管理器状态，请删除 `fileTree` 条目（关于如何在基于 Chromium 的浏览器中从本地存储删除某个键的指南见[此处](https://docs.devolutions.net/kb/general-knowledge-base/clear-browser-local-storage/clear-chrome-local-storage/)）。你可以通过传入 `useSavedState: false` 作为参数来禁用此行为。

## 自定义

大部分配置可以通过向 `Explorer()` 传入选项来完成。

例如，默认配置如下所示：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
    options:
      title: Explorer
      folderClickBehavior: collapse # "link" 表示导航，"collapse" 表示切换
      folderDefaultState: collapsed # "collapsed" 或 "open"
      useSavedState: true
    layout:
      position: left
      priority: 50
```

对于自定义排序、过滤和映射函数等高级选项，请在 `quartz.ts` 中使用 TS 覆盖：

```ts title="quartz.ts"
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"
import * as ExternalPlugin from "./.quartz/plugins"

// 高级用法：传入无法在 YAML 中表达的回调函数
ExternalPlugin.Explorer({
  sortFn: (a, b) => {
    /* ... */
  },
  filterFn: (node) => {
    /* ... */
  },
  mapFn: (node) => {
    /* ... */
  },
  order: ["filter", "map", "sort"],
})

const config = await loadQuartzConfig()
export default config
export const layout = await loadQuartzLayout()
```

> [!info] 覆盖机制如何工作
> 当你在 `quartz.ts` 中调用 `ExternalPlugin.Explorer({...})` 时，这些选项会被记录，并在构建过程中实例化组件时与 YAML 配置合并。在 `quartz.ts` 中设置的选项优先于 `quartz.config.yaml` 中的选项，优先级顺序为：`plugin defaults < YAML options < quartz.ts overrides`。
>
> 如果你有两个导出相同名称的插件（例如通过 `--name` 安装的两个不同的 Explorer 插件），可以使用 `plugins` 映射来消除歧义：
>
> ```ts title="quartz.ts"
> import * as ExternalPlugin from "./.quartz/plugins"
> ExternalPlugin.plugins["my-explorer"].Explorer({ mapFn: ... })
> ```

传入你自己的选项时，如果希望某个字段保留默认值，可以省略其中任意或全部字段。

想进一步自定义吗？

- 移除资源管理器：从 `quartz.config.yaml` 中删除 `explorer` 条目，或设置 `enabled: false`
  - （可选）：移除资源管理器组件后，你可以把 [[table of contents | 目录]] 组件移回布局的 `left` 部分
- 修改 `sort`、`filter` 和 `map` 的行为：详见 [[#高级自定义]]

## 高级自定义

该组件允许你完全自定义其所有行为。你可以传入自定义的 `sort`、`filter` 和 `map` 函数。
所有可以传入的函数都作用于 `FileTrieNode` 类，该类具有以下属性：

```ts title="@quartz-community/explorer"
class FileTrieNode {
  isFolder: boolean
  children: Array<FileTrieNode>
  data: ContentDetails | null
}
```

```ts
export type ContentDetails = {
  slug: FullSlug
  title: string
  links: SimpleSlug[]
  tags: string[]
  content: string
}
```

所有可以传入的函数都是可选的。默认情况下只会使用 `sort` 函数：

```ts title="Default sort function"
// 排序顺序：文件夹在前，文件在后。文件夹和文件分别按字母顺序排序
ExternalPlugin.Explorer({
  sortFn: (a, b) => {
    if ((!a.isFolder && !b.isFolder) || (a.isFolder && b.isFolder)) {
      return a.displayName.localeCompare(b.displayName, undefined, {
        numeric: true,
        sensitivity: "base",
      })
    }

    if (!a.isFolder && b.isFolder) {
      return 1
    } else {
      return -1
    }
  },
})
```

---

你可以为 `sortFn`、`filterFn` 和 `mapFn` 传入自己的函数。所有函数都会按照 `order` 选项给出的顺序执行（参见 [[#自定义]]）。这些函数的行为与其 `Array.prototype` 上对应的同名方法类似，区别在于它们会就地修改整个 `FileNode` 树，而不是返回一个新的。

有关如何使用 `sort`、`filter` 和 `map` 的更多信息，可以查看 [Array.prototype.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)、[Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 和 [Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)。

类型定义如下所示：

```ts
type SortFn = (a: FileTrieNode, b: FileTrieNode) => number
type FilterFn = (node: FileTrieNode) => boolean
type MapFn = (node: FileTrieNode) => void
```

## 基础示例

这些示例展示了 `sort`、`map` 和 `filter` 的基本用法。

### 使用 `sort` 把文件排在前面

使用这个示例后，资源管理器会按字母顺序对所有内容排序。

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
    options:
      # 简单选项写在 YAML 中
      title: Explorer
      folderDefaultState: collapsed
```

自定义排序函数需要使用 TS 覆盖：

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  sortFn: (a, b) => {
    return a.displayName.localeCompare(b.displayName)
  },
})
```

### 修改显示名称（`map`）

使用这个示例后，所有 `FileNodes`（文件夹 + 文件）的显示名称都会被转换为全大写。

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  mapFn: (node) => {
    node.displayName = node.displayName.toUpperCase()
    return node
  },
})
```

> [!note]
> `mapFn`、`filterFn` 和 `sortFn` 选项需要 JavaScript 回调函数，无法在 YAML 中表达。请对这些选项使用 TS 覆盖。

### 移除一组元素（`filter`）

使用这个示例，你可以通过提供一个要排除的文件夹/文件数组，从资源管理器中移除元素。
请注意，此示例基于标题进行过滤，但你也可以通过 slug 或 `FileTrieNode` 上可用的任何其他字段来实现。

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  filterFn: (node) => {
    // 包含所有要过滤掉的名称的集合
    const omit = new Set(["authoring content", "tags", "advanced"])

    // 也可以使用 node.slug，或 node.data 上的任何内容
    // 注意，node.data 只对磁盘上实际存在的文件存在
    //（例如没有关联 index.md 的隐式文件夹节点）
    return !omit.has(node.displayName.toLowerCase())
  },
})
```

### 按标签移除文件

你可以通过 `node.data.tags` 访问文件的标签。

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  filterFn: (node) => {
    // 排除带有 "explorerexclude" 标签的文件
    return node.data?.tags?.includes("explorerexclude") !== true
  },
})
```

### 显示资源管理器中的每个元素

默认情况下，资源管理器会过滤掉 `tags` 文件夹。
要覆盖默认的过滤函数，你可以把过滤函数设置为 `undefined`。

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  filterFn: undefined, // 不应用任何过滤函数，所有文件和文件夹都会显示
})
```

## 高级示例

> [!tip]
> 编写更复杂的函数时，`quartz.ts` 文件可能会变得非常拥挤。
> 你可以把排序函数定义在组件之外，
> 再把它传进去，以此解决这个问题。
>
> ```ts title="quartz.ts"
> import * as ExternalPlugin from "./.quartz/plugins"
> import type { ExplorerOptions } from "./.quartz/plugins"
>
> const mapFn: ExplorerOptions["mapFn"] = (node) => {
>   // 在这里实现你的函数
> }
> const filterFn: ExplorerOptions["filterFn"] = (node) => {
>   // 在这里实现你的函数
> }
> const sortFn: ExplorerOptions["sortFn"] = (a, b) => {
>   // 在这里实现你的函数
> }
>
> ExternalPlugin.Explorer({
>   // …… 你的其他选项
>   mapFn,
>   filterFn,
>   sortFn,
> })
> ```

### 添加 emoji 前缀

要添加 emoji 前缀（文件夹用 📁，文件用 📄），你可以在 `quartz.ts` 中使用映射函数：

```ts title="quartz.ts (override)"
ExternalPlugin.Explorer({
  mapFn: (node) => {
    if (node.isFolder) {
      node.displayName = "📁 " + node.displayName
    } else {
      node.displayName = "📄 " + node.displayName
    }
  },
})
```
