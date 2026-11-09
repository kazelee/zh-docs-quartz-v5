---
date: 2026-09-20
title: 未列出页面 (UnlistedPages)
description: 将页面从导航和索引中隐藏，同时仍然发布它们。
tags:
  - plugin/transformer
image:
new-in-v5: true
repository: "[quartz-community/unlisted-pages](https://github.com/quartz-community/unlisted-pages)"
enabled: true
required: false
---

零配置转换器，让页面 frontmatter 中的 `unlisted: true` 成为退出站点上所有列表展示位置的正式方式。该页面仍会作为 HTML 输出，并可通过直接 URL 访问，但不会出现在 `contentIndex.json`、RSS、站点地图、关系图谱、资源管理器、搜索、反向链接、近期笔记、文件夹列表和标签列表中。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 用法

在任意页面的 frontmatter 中添加 `unlisted` 字段：

```yaml
---
title: My Draft
unlisted: true
---
```

如此即可。每个遵循 `file.data.unlisted` 约定的 Quartz v5 插件随后都会隐藏该页面。

## 各插件的行为

| 插件 | `unlisted: true` 时的行为 |
| -------------- | -------------------------------------------------------------------------- |
| `ContentIndex` | 页面不出现在 `contentIndex.json`、`sitemap.xml` 和 RSS 订阅源中。 |
| `Search` | 页面不出现在搜索结果中（数据来自 `contentIndex.json`）。 |
| `Graph` | 页面不出现在关系图谱的节点和边中（数据来自 `contentIndex.json`）。 |
| `Explorer` | 页面不出现在侧边栏文件树中（数据来自 `contentIndex.json`）。 |
| `Backlinks` | 页面绝不会作为反向链接来源出现在其他页面上。 |
| `RecentNotes` | 页面不出现在近期笔记列表中。 |
| `FolderPage` | 页面不出现在文件夹列表和文件夹发现中。 |
| `TagPage` | 页面不出现在标签发现和标签列表中。 |

在每种情况下，页面的 HTML 仍会被输出，并可通过直接 URL 访问。

## 配置

零选项。只需启用即可。

```yaml title="quartz.config.yaml"
- source: github:quartz-community/unlisted-pages
  enabled: true
```

## 与 [[EncryptedPages]] 的交互

当设置了 `unlistWhenEncrypted: true` 选项，或页面 frontmatter 中有 `unlisted: true` 时，[[EncryptedPages]] 插件也会设置 `file.data.unlisted`。这两个插件可以良好地组合使用：

- 如果只安装 `UnlistedPages`：任何 frontmatter 中带有 `unlisted: true` 的页面都会从列表展示位置中隐藏。加密是独立的。
- 如果只安装 `EncryptedPages`：`unlisted: true` 只对同时已加密（设有密码）的页面生效。带有 `unlisted: true` 的未加密页面会被静默忽略。
- 如果两者都安装：`unlisted: true` 对每个页面都生效，无论是否加密。对于使用加密页面的站点，这是推荐的配置。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.UnlistedPages()`。
- 来源：[`quartz-community/unlisted-pages`](https://github.com/quartz-community/unlisted-pages)
- 安装：`npx quartz plugin add github:quartz-community/unlisted-pages`
