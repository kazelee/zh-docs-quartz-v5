---
title: 未找到页面 (NotFoundPage)
tags:
  - plugin/pageType
image:
---

该插件为失效或不存在的 URL 输出 404（Not Found）页面。它使用 `minimal` [[layout#Page Frames|页面框架]]（没有侧边栏，也没有页头或 beforeBody 装饰——只有内容和页脚）来呈现简洁的错误页面。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件没有配置选项。

## API

- 类别：页面类型
- 函数名：`Plugin.NotFoundPage()`（内部插件）。
- 来源：[`quartz/plugins/pageTypes/404.ts`](https://github.com/jackyzha0/quartz/blob/v5/quartz/plugins/pageTypes/404.ts)
