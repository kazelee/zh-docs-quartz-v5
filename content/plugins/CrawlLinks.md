---
date: 2026-09-20
title: 链接爬取 (CrawlLinks)
description: 解析并处理内部链接。不建议移除。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/crawl-links](https://github.com/quartz-community/crawl-links)"
enabled: true
required: true
---

本插件解析链接并处理它们，使其指向正确的位置。嵌入的链接（如图片）也需要它。更多信息请参见 [[Obsidian compatibility]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

本插件接受以下配置选项：

- `markdownLinkResolution`：设置解析 Markdown 路径的策略，可以是 `"absolute"`（默认值）、`"relative"` 或 `"shortest"`。此处应使用与 [[Obsidian compatibility|Obsidian]] 中相同的设置。
  - `absolute`：相对于内容文件夹根目录的路径。
  - `relative`：相对于你当前链接所在文件的路径。
  - `shortest`：文件名。如果这不足以标识该文件，请使用完整的绝对路径。
- `prettyLinks`：若为 `true`（默认值），则通过移除文件夹路径来简化链接，使其更易读（例如 `folder/deeply/nested/note` 会变为 `note`）。
- `openLinksInNewTab`：若为 `true`，则配置外部链接在新标签页中打开。默认为 `false`。
- `lazyLoad`：若为 `true`，则为资源元素（`img`、`video` 等）添加懒加载，以改善页面加载性能。默认为 `false`。
- `externalLinkIcon`：当为 `true`（默认值）时，在外部链接旁添加一个图标，以便在视觉上将其与内部链接区分开。
- `disableBrokenWikilinks`：若为 `true`，解析后 slug 不在站点中的内部链接（即在 `markdownLinkResolution` 下没有匹配文件）会在 `internal` 之外额外获得一个 `broken` CSS 类，从而可以单独设置样式。默认为 `false`。该选项对维基链接和普通 Markdown 链接都生效，因为在本插件运行时，两者都是无法区分的 `<a>` 元素。

> [!warning]
> 不建议移除此插件，这很可能导致页面出错。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.CrawlLinks()`。
- 源码：[`quartz-community/crawl-links`](https://github.com/quartz-community/crawl-links)
- 安装：`npx quartz plugin add github:quartz-community/crawl-links`
