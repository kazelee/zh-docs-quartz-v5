---
title: 静态资源 (Assets)
tags:
  - plugin/emitter
image: https://images.unsplash.com/photo-1526304640581-d334cdbbf45e
---

该插件输出内容文件夹中所有非 Markdown 的静态资源（如图片、视频、HTML 等）。该插件遵循全局 [[configuration]] 中的 `ignorePatterns`。

注意，之后所有静态资源都可以通过其路径在你生成的站点上访问，即：`host.me/path/to/static.pdf`

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件没有配置选项。

## API

- 类别：输出器
- 函数名：`Plugin.Assets()`（内部插件）。
- 来源：[`quartz/plugins/emitters/assets.ts`](https://github.com/jackyzha0/quartz/blob/v5/quartz/plugins/emitters/assets.ts)。
