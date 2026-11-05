---
title: 布局
---

某些输出器还可以输出 [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML) 文件。为便于自定义，这些输出器允许你完全重新排列页面的布局。

在 v5 中，布局在 `quartz.config.yaml` 中定义。每个插件通过 `layout.position` 和 `layout.priority` 字段控制自己的布局位置。顶层的 `layout` 部分提供两种额外机制：

- `layout.groups` 定义 flex 容器（如 `toolbar`），将多个组件组合到同一行或同一列。详见 [[layout-components]]。
- `layout.byPageType` 包含按页面类型（content、folder、tag、404）对 beforeBody、left、right 各区域的覆盖，以及可选的 `template`，用于控制页面的 [[#Page Frames|页面框架]]。

每个页面由多个不同的区域组成，这些区域包含 `QuartzComponents`。下面的代码片段列出了所有可以向其中添加组件的有效区域：

```typescript title="quartz/cfg.ts"
export interface FullPageLayout {
  head: QuartzComponent // 单个组件
  header: QuartzComponent[] // 水平排列
  beforeBody: QuartzComponent[] // 垂直排列
  pageBody: QuartzComponent // 单个组件
  afterBody: QuartzComponent[] // 垂直排列
  left: QuartzComponent[] // 在桌面和平板上垂直排列，在移动端水平排列
  right: QuartzComponent[] // 在桌面上垂直排列，在平板和移动端水平排列
  footer: QuartzComponent[] // 垂直排列
}
```

它们对应页面的以下部分：

| 布局                            | 预览                                |
| ------------------------------- | ----------------------------------- |
| 桌面（宽度 > 1200px）           | ![[quartz-layout-desktop.png\|800]] |
| 平板（800px < 宽度 < 1200px）   | ![[quartz-layout-tablet.png\|800]]  |
| 移动端（宽度 < 800px）          | ![[quartz-layout-mobile.png\|800]]  |

> [!note]
> 还有两个布局字段_未_在上图中展示。
>
> 1. `head` 是单个组件，负责渲染 HTML 中的 `<head>` [标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head)。它在页面上不可见，只负责文档的元数据，如标签页标题、脚本和样式。
> 2. `header` 是一组水平排列的组件，出现在 `beforeBody` 区域_之前_。你可以在插件配置中设置 `layout.position: header` 把组件放入 header。这样可以实现类似 Quartz 3 顶部栏的布局，包含标题、搜索栏和深色模式切换。

布局组件在 `quartz.config.yaml` 的 `layout` 部分配置。插件声明自己的位置和优先级，布局系统会自动安排它们：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
    layout:
      position: left
      priority: 50
  - source: github:quartz-community/graph
    enabled: true
    layout:
      position: right
      priority: 10
  - source: github:quartz-community/search
    enabled: true
    layout:
      position: left
      priority: 20
  - source: github:quartz-community/backlinks
    enabled: true
    layout:
      position: right
      priority: 30
  - source: github:quartz-community/article-title
    enabled: true
    layout:
      position: beforeBody
      priority: 10
  - source: github:quartz-community/content-meta
    enabled: true
    layout:
      position: beforeBody
      priority: 20
  - source: github:quartz-community/tag-list
    enabled: true
    layout:
      position: beforeBody
      priority: 30
  - source: github:quartz-community/darkmode
    enabled: true
    layout:
      position: header
      priority: 10
  - source: github:quartz-community/footer
    enabled: true
    options:
      links:
        GitHub: https://github.com/jackyzha0/quartz
        Discord Community: https://discord.gg/cRFFHYye7t
    layout:
      position: footer
      priority: 50

layout:
  groups:
    toolbar:
      direction: row
      gap: 0.5rem
  byPageType:
    content: {}
    folder:
      exclude:
        - reader-mode
      positions:
        right: []
    tag:
      exclude:
        - reader-mode
      positions:
        right: []
    "404":
      positions:
        beforeBody: []
        left: []
        right: []
```

### 条件渲染

插件可以在其 layout 块中指定 `condition` 来控制何时出现。这里使用内置预设：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/breadcrumbs
    enabled: true
    layout:
      position: beforeBody
      priority: 5
      condition: not-index
```

可用的条件：

| 条件        | 效果                                                 |
| ----------- | ---------------------------------------------------- |
| `not-index` | 在根索引页隐藏，在其他所有页面显示                   |
| `has-tags`  | 仅在 frontmatter 中包含标签的页面上显示              |

关于条件渲染和显示选项的更多细节，参见 [[layout-components]]。

如需使用 TypeScript 进行高级布局覆盖（例如自定义组件包装或条件逻辑），你可以使用 `quartz.ts` 中的 TS 覆盖：

```ts title="quartz.ts"
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"

const config = await loadQuartzConfig()
export default config
export const layout = await loadQuartzLayout({
  defaults: {
    // 覆盖所有页面类型的默认布局
  },
  byPageType: {
    content: {
      // 仅覆盖内容页面的布局
    },
    folder: {
      // 仅覆盖文件夹页面的布局
    },
  },
})
```

在 `defaults` 中定义的字段可以被 `byPageType` 中的具体条目覆盖。

社区组件插件通过 `npx quartz plugin add github:quartz-community/<name>` 安装。内置的布局工具（Flex、MobileOnly、DesktopOnly 等）参见 [[layout-components]]。

如果你有兴趣进一步自定义 Quartz 的行为，也可以查看 [[creating components]] 指南。

### 页面框架

页面框架控制页面的整体 HTML 结构 —— 具体来说，就是布局槽位（侧边栏、header、内容、页脚）如何在页面外壳内排列。不同的页面类型可以使用不同的框架，从而产生根本不同的布局。

Quartz 内置三种框架：

| 框架         | 说明                                                                                                                                                            | 使用者                                      |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| `default`    | 三栏布局：左侧边栏、中间内容（header、beforeBody、content、afterBody）、右侧边栏和页脚。这是 Quartz 的标准布局。                                                | ContentPage、FolderPage、TagPage、BasesPage |
| `full-width` | 无侧边栏。单一居中列横跨整个宽度，包含 header、content、afterBody 和页脚。                                                                                       | —                                           |
| `minimal`    | 无侧边栏，也没有 header 或 beforeBody 装饰。仅包含内容和页脚。                                                                                                   | NotFoundPage (404)                          |

插件也可以提供自己的框架。例如，`canvas-page` 插件提供了一个 `"canvas"` 框架，可提供带可切换侧边栏的全屏画布。

#### 框架如何解析

每种页面类型都可以在其插件源码中通过 `frame` 属性声明默认框架。解析顺序为：

1. **YAML 配置覆盖**：`quartz.config.yaml` 中的 `layout.byPageType.<name>.template`
2. **插件注册的框架**：插件通过 Frame Registry 注册的框架（从插件的 `frames` 导出加载）
3. **插件声明**：在页面类型插件源码中设置的 `frame` 属性
4. **回退**：`"default"`

例如，要覆盖 canvas 页面以使用 minimal 框架：

```yaml title="quartz.config.yaml"
layout:
  byPageType:
    canvas:
      template: minimal
```

#### 自定义框架

提供自定义框架有两种方式：

**1. 插件提供的框架（推荐用于可复用的框架）：**

插件可以通过在 `package.json` 中声明并从 `./frames` 子路径导出，来提供自己的框架。详见 [[making plugins#Providing Custom Frames|插件指南]]。安装带有框架的插件后，其框架会自动注册到 Frame Registry，并可按名称使用。

**2. 核心框架（用于特定项目的框架）：**

你也可以直接在 `quartz/components/frames/` 中创建框架：实现 `PageFrame` 接口，并在 `quartz/components/frames/index.ts` 中注册该框架。完整的 `PageFrame` 接口见 [[architecture|架构概览]]。

框架会以 `data-frame` 属性的形式应用到 `.page` 元素上，你可以在 CSS 中针对它编写样式：

```scss
.page[data-frame="my-frame"] > #quartz-body {
  /* 自定义网格布局 */
}
```

框架的 CSS 应使用 `[data-frame="name"]` 选择器限定作用范围，以避免与其他框架冲突。

### 布局断点

Quartz 会根据查看网站的屏幕宽度采用不同的布局。

布局断点可以在 `variables.scss` 中配置。

- `mobile`：屏幕宽度低于此值将使用移动端布局。
- `desktop`：屏幕宽度高于此值将使用桌面布局。
- 宽度介于 `mobile` 和 `desktop` 之间的屏幕将使用平板布局。

```scss
$breakpoints: (
  mobile: 800px,
  desktop: 1200px,
);
```

### 样式

大多数有意义的样式更改（如配色方案和字体）都可以直接通过 [[configuration#General Configuration|通用配置]] 选项完成。不过，如果你想进行更复杂的样式更改，可以编写自己的样式。Quartz 使用 [Sass](https://sass-lang.com/guide/) 进行样式处理。

基础样式表位于 `quartz/styles/base.scss`，你可以在 `quartz/styles/custom.scss` 中编写自己的样式。

> [!note]
> 有些组件也可能自带样式！社区插件会打包自己的样式。如果你想为特定组件自定义样式，请仔细查看该组件的定义，了解其样式是如何定义的。
