---
date: 2026-09-20
title: 面包屑导航 (Breadcrumbs)
description: 面包屑导航路径。
tags:
  - plugin/component
image:
repository: "[quartz-community/breadcrumbs](https://github.com/quartz-community/breadcrumbs)"
enabled: true
required: false
---

导航面包屑路径。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

详细用法信息请参阅 [[features/Breadcrumbs]]。

> [TranslatorNote] 译者注：原文这里是 `[[plugins/Breadcrumbs]]`，但这样就自指了，其实应该是 `[[features/Breadcrumbs]]`，目前官方网站已经修复了，这里也做相应的更改。

## 配置

该插件接受以下配置选项：

- `spacerSymbol`：面包屑项之间使用的符号。默认为 `"❯"`。
- `rootName`：根页面的名称。默认为 `Home`。
- `resolveFrontmatterTitle`：是否为面包屑项使用 `title` frontmatter 字段。默认为 `true`。
- `showCurrentPage`：是否在面包屑路径中显示当前页面。默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/breadcrumbs
  enabled: true
  options:
    spacerSymbol: "❯"
    rootName: Home
    resolveFrontmatterTitle: true
    showCurrentPage: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Breadcrumbs()`。
- 来源：[`quartz-community/breadcrumbs`](https://github.com/quartz-community/breadcrumbs)
- 安装：`npx quartz plugin add github:quartz-community/breadcrumbs`
