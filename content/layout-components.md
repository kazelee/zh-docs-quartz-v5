---
date: 2026-09-20
title: 高阶布局组件
---

Quartz 提供了若干高阶组件，用于辅助布局组合和响应式设计。这些组件包装其他组件，以添加额外功能或修改其行为。

大多数常见用例都可以通过布局属性直接在 `quartz.config.yaml` 中配置。对于需要自定义逻辑的高级场景，可以使用 `quartz.ts` 中的 TS 覆盖方式。

## `Flex` 组件

`Flex` 组件创建一个[弹性盒子布局](https://developer.mozilla.org/en-US/docs/Web/CSS/flex)，可以用多种方式排列子组件。它在创建响应式布局、以及按行或按列组织组件时特别有用。

### YAML 配置

在 YAML 中，flex 布局通过 **groups** 创建。在顶层 `layout.groups` 部分定义一个组，然后通过插件的 `layout.group` 属性把插件分配到该组：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/search
    enabled: true
    layout:
      position: left
      priority: 20
      group: toolbar
      groupOptions:
        grow: true # Search 会伸展以填满可用空间
  - source: github:quartz-community/darkmode
    enabled: true
    layout:
      position: left
      priority: 30
      group: toolbar # Darkmode 保持其自然尺寸
  - source: github:quartz-community/reader-mode
    enabled: true
    layout:
      position: left
      priority: 35
      group: toolbar

layout:
  groups:
    toolbar:
      direction: row
      gap: 0.5rem
```

每个插件条目上的 `groupOptions` 字段支持以下 flex item 属性：

| 选项      | 类型                                                            | 说明                                 |
| --------- | --------------------------------------------------------------- | --------------------------------------------------------- |
| `grow`    | `boolean`                                                       | 组件是否应伸展以填满可用空间         |
| `shrink`  | `boolean`                                                       | 组件在需要时是否应收缩               |
| `basis`   | `string`                                                        | 组件的主轴初始尺寸（例如 `"200px"`） |
| `order`   | `number`                                                        | 在 flex 容器中的排列顺序             |
| `align`   | `"start"` \| `"end"` \| `"center"` \| `"stretch"`               | 交叉轴对齐方式                       |
| `justify` | `"start"` \| `"end"` \| `"center"` \| `"between"` \| `"around"` | 主轴对齐方式                         |

顶层 `layout.groups` 部分配置 flex 容器本身：

| 选项        | 类型                                                           | 说明                                    |
| ----------- | -------------------------------------------------------------- | ----------------------------------------- |
| `direction` | `"row"` \| `"row-reverse"` \| `"column"` \| `"column-reverse"` | flex 方向                               |
| `wrap`      | `"nowrap"` \| `"wrap"` \| `"wrap-reverse"`                     | flex 换行行为                           |
| `gap`       | `string`                                                       | flex 项之间的间距（例如 `"0.5rem"`）    |

### TS 覆盖

如需完全通过代码控制，请使用 `quartz.ts` 中的 `Component.Flex()` 包装器：

```ts title="quartz.ts (override)"
Component.Flex({
  components: [
    {
      Component: Plugin.Search(),
      grow: true, // Search 会伸展以填满可用空间
    },
    { Component: Plugin.Darkmode() }, // Darkmode 保持其自然尺寸
  ],
  direction: "row",
  gap: "1rem",
})
```

```typescript
type FlexConfig = {
  components: {
    Component: QuartzComponent
    grow?: boolean
    shrink?: boolean
    basis?: string
    order?: number
    align?: "start" | "end" | "center" | "stretch"
    justify?: "start" | "end" | "center" | "between" | "around"
  }[]
  direction?: "row" | "row-reverse" | "column" | "column-reverse"
  wrap?: "nowrap" | "wrap" | "wrap-reverse"
  gap?: string
}
```

> [!note] 覆盖行为
> `Flex` 内部的组件会获得一个额外的 CSS 类 `flex-component`，它会添加 `display: flex` 属性。如果你想覆盖这一行为，可以在自定义 CSS 文件中为该组件的 CSS 类添加 `display` 属性。
>
> ```scss
> .flex-component {
>   display: block; // 或任何其他 display 类型
> }
> ```

## `MobileOnly` / `DesktopOnly` 组件

这些组件控制插件在移动端或桌面端设备上是否可见。这在创建响应式布局时很有用，可以让某些组件只在特定屏幕尺寸下出现。

### YAML 配置

在 YAML 中，使用插件 layout 条目上的 `display` 属性：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/table-of-contents
    enabled: true
    layout:
      position: right
      priority: 20
      display: desktop-only # 仅在桌面端可见
```

可用的 `display` 值：

| 值             | 说明                                 |
| -------------- | ------------------------------------- |
| `all`          | 在所有屏幕尺寸下可见（默认）         |
| `mobile-only`  | 仅在移动端设备可见                   |
| `desktop-only` | 仅在桌面端设备可见                   |

### TS 覆盖

使用 TS 覆盖方式时，请使用 `Component.MobileOnly()` 或 `Component.DesktopOnly()` 包装器：

```ts title="quartz.ts (override)"
Component.MobileOnly(Component.Spacer())
```

```ts title="quartz.ts (override)"
Component.DesktopOnly(Plugin.TableOfContents())
```

## `ConditionalRender` 组件

`ConditionalRender` 组件根据页面属性有条件地渲染插件。这在创建动态布局时很有用，可以让组件只在特定条件下出现。

### YAML 配置

在 YAML 中，使用插件 layout 条目上的 `condition` 属性。Quartz 提供了多个内置条件预设：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/breadcrumbs
    enabled: true
    layout:
      position: beforeBody
      priority: 5
      condition: not-index # 在根索引页隐藏面包屑导航
```

可用的内置条件：

| 条件            | 说明                                             |
| --------------- | ----------------------------------------------------- |
| `not-index`     | 仅当页面不是根 `index.md` 时渲染                 |
| `has-tags`      | 仅当页面的 frontmatter 中包含标签时渲染          |
| `has-backlinks` | 仅当页面有反向链接时渲染                         |
| `has-toc`       | 仅当页面有目录时渲染                             |

### TS 覆盖

对于内置预设未覆盖的自定义条件，请使用 `quartz.ts` 中的 `Component.ConditionalRender()`：

```ts title="quartz.ts (override)"
Component.ConditionalRender({
  component: Plugin.Search(),
  condition: (props) => props.displayClass !== "fullpage",
})
```

```typescript
type ConditionalRenderConfig = {
  component: QuartzComponent
  condition: (props: QuartzComponentProps) => boolean
}
```

> [!tip]
> 你也可以在插件的初始化代码中调用 `registerCondition()` 注册自定义条件，以便在 YAML 中使用。更多细节参见 [[making plugins]]。
