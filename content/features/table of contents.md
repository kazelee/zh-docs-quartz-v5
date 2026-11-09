---
date: 2026-09-20
title: "目录"
tags:
  - component
  - feature/transformer
---

Quartz 可以根据每个页面上的标题列表自动生成目录（TOC）。它还会通过用不同颜色高亮你已滚动过的标题，向你显示当前在页面上的滚动位置。

你可以通过在该页面的 frontmatter 中添加 `enableToc: false` 来隐藏该页面的目录。

默认情况下，目录显示从 H1（`# Title`）到 H3（`### Title`）的所有标题，并且只有当页面上有多个标题时才会显示。

## 自定义

目录是 [[TableOfContents]] 插件的功能。有关更多自定义选项，请参阅该插件页面。

它还需要 `TableOfContents` 组件，该组件默认显示在右侧边栏中。你可以通过自定义 [[layout]] 来更改这一点。TOC 组件可以使用 `layout` 参数进行配置，该参数可以是 `modern`（默认）或 `legacy`。
