---
date: 2026-09-20
title: Obsidian 风格 Markdown (ObsidianFlavoredMarkdown)
description: Obsidian 特有的 Markdown 扩展（维基链接、标注块、高亮、标签、嵌入）。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/obsidian-flavored-markdown](https://github.com/quartz-community/obsidian-flavored-markdown)"
enabled: true
required: false
---

本插件提供对 [[Obsidian compatibility]] 的支持。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件接受以下配置选项：

- `comments`：若为 `true`（默认值），则启用对 `%%` 风格 Obsidian 注释块的解析。
- `highlight`：若为 `true`（默认值），则启用对内容中 `==` 风格高亮的解析。
- `wikilinks`：若为 `true`（默认值），则将 [[wikilinks]] 转换为普通链接。
- `callouts`：若为 `true`（默认值），则添加对 [[callouts|callout]] 块的支持，用于强调内容。
- `mermaid`：若为 `true`（默认值），则启用 Markdown 文件中的 [[Mermaid diagrams|Mermaid 图表]]渲染。
- `parseTags`：若为 `true`（默认值），则解析并链接内容中的标签。
- `parseBlockReferences`：若为 `true`（默认值），则处理块引用，链接到特定的内容块。
- `enableInHtmlEmbed`：若为 `true`，则允许直接在 HTML 中嵌入内容。默认值为 `false`。
- `enableYouTubeEmbed`：若为 `true`（默认值），则启用使用外部图片 Markdown 语法嵌入 YouTube 视频和播放列表。
- `enableTweetEmbed`：若为 `true`（默认值），则启用从 Twitter/X URL 以静态引用块的形式嵌入推文。
- `enableVideoEmbed`：若为 `true`（默认值），则启用视频文件的嵌入。
- `enableCheckbox`：若为 `true`，则添加对内容中交互式复选框的支持，包括自定义任务字符（例如 `- [?]`、`- [!]`、`- [/]`）。默认值为 `false`。
- `enableObsidianUri`：若为 `true`（默认值），则用 CSS 类和 data 属性标记 `obsidian://` 协议链接，以便自定义样式。

> [!note]
> `disableBrokenWikilinks` 选项以前位于本插件。它已移至 [[CrawlLinks]]，后者负责链接解析，并能在判断链接是否失效时遵循配置的 `markdownLinkResolution` 策略。从早期 Quartz v5 beta 版本升级的用户应将此选项从 `ObsidianFlavoredMarkdown` 移到 `CrawlLinks`。

> [!warning]
> 如果你使用 [[Obsidian compatibility|Obsidian]] 创作内容，请不要移除此插件！

## API

- 类别：转换器
- 函数名：`ExternalPlugin.ObsidianFlavoredMarkdown()`。
- 来源：[`quartz-community/obsidian-flavored-markdown`](https://github.com/quartz-community/obsidian-flavored-markdown)
- 安装：`npx quartz plugin add github:quartz-community/obsidian-flavored-markdown`
