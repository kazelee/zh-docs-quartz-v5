---
date: 2026-09-20
title: 静态文件 (Static)
tags:
  - plugin/emitter
image:
---

本插件输出 Quartz 所需的所有静态资源。例如用于字体和需要稳定位置的图片，如横幅和图标。该插件会遵循全局 [[configuration]] 中的 `ignorePatterns`。

> [!important]
> 这与 [[Assets]] 不同。来自 [[Static]] 插件的资源位于 `quartz/static` 下，而 [[Assets]] 会渲染 `content` 下的所有静态资源，用于你的 markdown 内容直接引用的图片、视频、音频等。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

本插件没有配置选项。

## API

- 类别：输出器
- 函数名：`Plugin.Static()`（内部插件）。
- 来源：[`quartz/plugins/emitters/static.ts`](https://github.com/jackyzha0/quartz/blob/v5/quartz/plugins/emitters/static.ts)。
