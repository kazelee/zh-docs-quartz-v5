---
date: 2026-09-20
title: 标签页面 (TagPage)
description: 为标签生成列表页面。
tags:
  - plugin/pageType
image:
repository: "[quartz-community/tag-page](https://github.com/quartz-community/tag-page)"
enabled: true
required: false
---

本插件是一个页面类型插件，会为内容中使用的每个标签输出专门的页面。它使用 `default` [[layout#页面框架|页面框架]]（带侧边栏的三栏布局）。更多信息请参阅 [[folder and tag listings]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

本插件接受以下配置选项：

- `numPages`：每个标签在显示「查看更多」链接之前可显示的最大页面数。默认为 `10`。
- `sort`：用于排序条目的 `(f1: QuartzPluginData, f2: QuartzPluginData) => number{:ts}` 类型的函数。默认按日期排序，日期相同时按字典序排序。需要通过 TS 覆盖实现。
- `prefixTags`：若为 `true`，生成的标签页面标题会加上「Tag: 」前缀（例如「Tag: recipes」）。默认为 `false`。

## API

- 类别：页面类型
- 函数名：`ExternalPlugin.TagPage()`。
- 来源：[`quartz-community/tag-page`](https://github.com/quartz-community/tag-page)
- 安装：`npx quartz plugin add github:quartz-community/tag-page`
