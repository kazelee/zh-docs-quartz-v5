---
date: 2026-09-20
title: 站点图标 (Favicon)
description: 输出站点图标。
tags:
  - plugin/emitter
image:
repository: "[quartz-community/favicon](https://github.com/quartz-community/favicon)"
enabled: true
required: false
---

该插件会将 `favicon.ico` 输出到 `public` 文件夹。它使用位于 `quartz/static` 文件夹中的 `icon.png` 生成站点图标。
插件会把 `icon.png` 调整为 48x48px，使其尽可能小。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件没有配置选项。

## API

- 类别：输出器
- 函数名：`ExternalPlugin.Favicon()`。
- 来源：[`quartz-community/favicon`](https://github.com/quartz-community/favicon)
- 安装：`npx quartz plugin add github:quartz-community/favicon`
