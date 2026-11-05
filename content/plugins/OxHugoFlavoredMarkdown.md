---
title: ox-hugo 风格 Markdown (OxHugoFlavoredMarkdown)
description: 针对 ox-hugo 导出的 Org-mode 文件的兼容性支持。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/ox-hugo](https://github.com/quartz-community/ox-hugo)"
enabled: false
required: false
---

本插件提供对 [ox-hugo](https://github.com/kaushalmodi/ox-hugo) 的兼容性支持。更多信息请参阅 [[OxHugo compatibility]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件接受以下配置选项：

- `wikilinks`：若为 `true`（默认值），则将 Hugo 的 `{{ relref }}` 短代码转换为 Quartz [[wikilinks]]。
- `removePredefinedAnchor`：若为 `true`（默认值），则从标题中去除预定义锚点。
- `removeHugoShortcode`：若为 `true`（默认值），则从内容中移除 Hugo 短代码语法（`{{}}`）。
- `replaceFigureWithMdImg`：若为 `true`（默认值），则将 `<figure/>` 替换为 `![]()`。
- `replaceOrgLatex`：若为 `true`（默认值），则将 Org-mode [[features/Latex|Latex]] 片段转换为 Quartz 兼容的 LaTeX，内联公式用 `$` 包裹，块级公式用 `$$` 包裹。

> [!warning]
> 虽然你可以将本插件与 [[ObsidianFlavoredMarkdown]] 一起使用，但不建议这样做，因为它可能以意料之外的方式改动文件。请谨慎使用。
>
> 如果你使用 `toml` frontmatter，请务必相应地配置 [[Frontmatter]] 插件。示例请参阅 [[OxHugo compatibility]]。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.OxHugoFlavoredMarkdown()`。
- 来源：[`quartz-community/ox-hugo`](https://github.com/quartz-community/ox-hugo)
- 安装：`npx quartz plugin add github:quartz-community/ox-hugo`
