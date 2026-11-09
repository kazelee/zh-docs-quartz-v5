---
date: 2026-09-20
title: 内容索引 (ContentIndex)
description: 生成站点地图、RSS 订阅源和内容索引。
tags:
  - plugin/emitter
image:
repository: "[quartz-community/content-index](https://github.com/quartz-community/content-index)"
enabled: true
required: false
---

本插件为你的站点输出 RSS 和 XML 站点地图。[[RSS Feed]] 让用户可以订阅你站点上的内容，站点地图则让搜索引擎更好地索引你的站点。该插件还会输出一个 `contentIndex.json` 文件，供搜索和关系图谱等动态前端组件使用。

本插件输出站点内容的综合索引，并生成站点地图、RSS 订阅源以及一个

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

本插件接受以下配置选项：

- `enableSiteMap`：若为 `true`（默认值），则生成站点地图 XML 文件（`sitemap.xml`），列出所有站点 URL，供搜索引擎发现内容。
- `enableRSS`：若为 `true`（默认值），则生成包含近期内容更新的 RSS 订阅源（`index.xml`）。
- `rssLimit`：定义 RSS 订阅源中包含的最大条目数，有助于聚焦最新或最相关的内容。默认为 `10`。
- `rssFullHtml`：若为 `true`，RSS 订阅源会包含每个页面渲染后的完整 HTML 内容。默认为 `false`。
- `rssSlug`：生成的 RSS 订阅源 XML 文件的 slug。默认为 `"index"`。
- `includeEmptyFiles`：若为 `true`（默认值），没有正文内容的内容文件也会被包含在生成的索引和资源中。

## API

- 类别：输出器
- 函数名：`ExternalPlugin.ContentIndex()`。
- 源码：[`quartz-community/content-index`](https://github.com/quartz-community/content-index)
- 安装：`npx quartz plugin add github:quartz-community/content-index`
