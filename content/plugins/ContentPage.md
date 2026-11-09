---
date: 2026-09-20
title: 内容页面 (ContentPage)
description: 为 Markdown 内容生成 HTML 页面。
tags:
  - plugin/pageType
image:
repository: "[quartz-community/content-page](https://github.com/quartz-community/content-page)"
enabled: true
required: false
---

本插件是 Quartz 框架的页面类型插件。它为每一份 Markdown 内容生成 HTML 页面。它输出整页的 [[layout]]，包括页眉、页脚和正文内容等。它使用 `default` [[layout#页面框架|页面框架]]（带侧边栏的三栏布局）。现在它配置在 `quartz.config.yaml` 的 `pageTypes` 部分。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#插件|配置]] 页面。

本插件没有配置选项。

## API

- 类别：页面类型
- 函数名：`ExternalPlugin.ContentPage()`。
- 源码：[`quartz-community/content-page`](https://github.com/quartz-community/content-page)
- 安装：`npx quartz plugin add github:quartz-community/content-page`
