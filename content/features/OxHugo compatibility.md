---
title: "OxHugo 兼容性"
tags:
  - feature/transformer
---

[org-roam](https://www.orgroam.com/) 是一个用于 [emacs](https://en.wikipedia.org/wiki/Emacs) 的纯文本个人知识管理系统。[ox-hugo](https://github.com/kaushalmodi/ox-hugo) 是一个 org 导出后端，可将 `org-mode` 文件导出为 [Hugo](https://gohugo.io/) 兼容的 Markdown。

由于 ox-hugo 生成的 Markdown 并非纯 Markdown，而是 Hugo 特有的，因此我们需要对它进行转换，以适配 Quartz。这由 [[OxHugoFlavoredMarkdown]] 插件完成。尽管该插件最初是考虑 `ox-hugo` 而编写的，但它应当适用于任何 Hugo 特有的 Markdown。

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/obsidian-flavored-markdown
    enabled: true
    order: 30
  - source: github:quartz-community/ox-hugo
    enabled: true
    order: 25 # 必须位于 obsidian-flavored-markdown 之前
  - source: github:quartz-community/github-flavored-markdown
    enabled: true
    order: 40
  - source: github:quartz-community/note-properties
    enabled: true
    options:
      delimiters: "+++"
      language: toml # 如果使用 toml frontmatter
    order: 5
```

对于 TS 覆盖的方式，请将覆盖放在 `quartz.ts` 中 `loadQuartzConfig()` 之前：

```ts title="quartz.ts (override)"
import * as ExternalPlugin from "./.quartz/plugins"

ExternalPlugin.NoteProperties({ delims: "+++", language: "toml" })
ExternalPlugin.OxHugoFlavouredMarkdown()
```

> [!note]
> 在 YAML 中，插件执行顺序由 `order` 字段控制。数字越小越先执行。请确保 `ox-hugo` 的 `order` 小于 `obsidian-flavored-markdown`。

## 用法

Quartz 默认不识别 `org-roam` 文件，因为它们不是 Markdown。你需要自行使用 `ox-hugo` 这样的外部工具将 `org-roam` 文件导出为 Markdown 内容并交给 Quartz，同时管理静态资源，使它们能在最终输出中可用。

## 配置

此功能由 [[OxHugoFlavoredMarkdown]] 插件提供。有关自定义选项，请参阅插件页面。
