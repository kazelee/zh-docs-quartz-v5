---
title: "面包屑导航"
tags:
  - component
---

面包屑导航通过列出页面的父级文件夹，提供了一种在站点内页面层级中导航的方式。

默认情况下，页面最顶部的元素就是面包屑导航栏（在本文页面顶部也能看到！）。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

## 自定义

大部分配置可以通过 `quartz.config.yaml` 中 breadcrumbs 插件条目的 `options` 部分完成。

例如，默认配置如下所示：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/breadcrumbs
    enabled: true
    options:
      spacerSymbol: "❯"
      rootName: Home
      resolveFrontmatterTitle: true
      showCurrentPage: true
    layout:
      position: beforeBody
      priority: 5
```

对于 TS 覆盖的方式：

```ts title="quartz.ts (override)"
// 必须放在 loadQuartzConfig() 之前
ExternalPlugin.Breadcrumbs({
  spacerSymbol: "❯",
  rootName: "Home",
  resolveFrontmatterTitle: true,
  showCurrentPage: true,
})
```

传入你自己的选项时，如果希望某个字段保留默认值，可以省略其中任意或全部字段。

你还可以通过修改 `quartz.config.yaml` 中插件条目的 `layout.position` 字段，调整面包屑导航的显示位置（参见 [[layout]]）。

想进一步自定义吗？

- 移除面包屑导航：从 `quartz.config.yaml` 中删除 `breadcrumbs` 条目，或设置 `enabled: false`。
- 安装：`npx quartz plugin add github:quartz-community/breadcrumbs`
- 源码：[`quartz-community/breadcrumbs`](https://github.com/quartz-community/breadcrumbs)
