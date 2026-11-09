---
date: 2026-09-20
title: 最近的笔记
tags: component
---

Quartz 可以根据一些过滤和排序条件生成一份最近的笔记列表。虽然该组件默认不包含在任何 [[layout]] 中，但你可以安装插件并在 `quartz.config.yaml` 中配置来添加它。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 自定义

大多数选项在 `quartz.config.yaml` 中插件条目的 `options` 部分配置：

- 修改标题（原为 "Recent notes"）：在 options 中设置 `title: "Recent writing"`
- 更改最近笔记的数量：在 options 中设置 `limit: 5`
- 显示笔记的标签（默认为 true）：在 options 中设置 `showTags: false`
- 隐藏列表中生成的标签页面（默认为 false）：在 options 中设置 `hideTagPages: true`。这会过滤掉 slug 位于常规 `tags/` 前缀下的任何页面。
- 隐藏列表中生成的文件夹索引页面（默认为 false）：在 options 中设置 `hideFolderPages: true`。这会过滤掉 slug 匹配 Quartz 文件夹路径约定（尾部斜杠或 `index` 后缀）的任何页面。
- 显示“查看更多”链接：在 options 中设置 `linkToMore: "tags/components"`。该字段应是存在页面的完整 slug。
- 自定义过滤：需要 TS 覆盖——在 `quartz.ts` 中向插件构造函数传入 `filter: someFilterFunction`。过滤函数应具有签名 `(f: QuartzPluginData) => boolean`。
- 自定义排序：需要 TS 覆盖——在 `quartz.ts` 中向插件构造函数传入 `sort: someSortFunction`。默认情况下，Quartz 会先按日期排序，再按字典序决定顺序。排序函数应具有签名 `(f1: QuartzPluginData, f2: QuartzPluginData) => number`。
- 安装：`npx quartz plugin add github:quartz-community/recent-notes`
- 源码：[`quartz-community/recent-notes`](https://github.com/quartz-community/recent-notes)
