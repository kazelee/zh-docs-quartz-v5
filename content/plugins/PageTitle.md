---
title: 页面标题 (PageTitle)
description: 将站点标题渲染为指向首页的链接。
tags:
  - plugin/component
image:
repository: "[quartz-community/page-title](https://github.com/quartz-community/page-title)"
enabled: true
required: false
---

本插件将全站页面标题（通过 [[configuration]] 中的 `pageTitle` 字段配置）渲染为指向首页的可点击链接。它通常出现在左侧边栏中。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件没有配置选项。显示的标题由 `quartz.config.yaml` 中的 `pageTitle` 字段控制。

## API

- 类别：组件
- 函数名：`ExternalPlugin.PageTitle()`。
- 来源：[`quartz-community/page-title`](https://github.com/quartz-community/page-title)
- 安装：`npx quartz plugin add github:quartz-community/page-title`
