---
date: 2026-09-20
title: 撰写内容
aliases:
  - "authoring content"
---

你的 Quartz 中的所有内容都应放在 `/content` 文件夹中。Quartz 首页的内容位于 `content/index.md`。如果你已经按照 [[installation|安装指南]] 操作，这个文件夹应该已经初始化好了。该文件夹中的任何 Markdown 都会被 Quartz 处理。

建议你使用 [Obsidian](https://obsidian.md/) 来编辑和维护你的 Quartz。它提供了好用的编辑器和图形界面，可以预览、编辑并链接你的本地文件和附件。

一切都设置好了吗？用 `npx quartz build --serve` 在本地预览站点，或查看 [[build|构建参考]] 了解更多选项。

## 语法

由于 Quartz 以 Markdown 文件作为撰写内容的主要方式，它完全支持 Markdown 语法。默认情况下，Quartz 还附带了一些语法扩展，例如 [Github Flavored Markdown](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)（脚注、删除线、表格、任务列表）和 [Obsidian Flavored Markdown](https://help.obsidian.md/Editing+and+formatting/Obsidian+Flavored+Markdown)（[[callouts]]、[[wikilinks]]）。

此外，Quartz 还允许你在笔记中指定称为 **frontmatter** 的额外元数据。

```md title="content/note.md"
---
title: Example Title
draft: false
tags:
  - example-tag
---

The rest of your content lives here. You can use **Markdown** here :)
```

Quartz 原生支持的一些常见 frontmatter 字段：

- `title`：页面标题。如果未提供，Quartz 会使用文件名作为标题。
- `description`：用于链接预览的页面描述。
- `permalink`：页面的自定义 URL，即使文件路径发生变化也会保持不变。
- `aliases`：这条笔记的其他名称。它是一个字符串列表。
- `tags`：这条笔记的标签。
- `draft`：是否发布该页面。这是在 Quartz 中 [[private pages|将页面设为私有]] 的一种方式。
- `date`：表示笔记发布日期的字符串。通常使用 `YYYY-MM-DD` 格式。

完整的 frontmatter 列表见 [[Frontmatter]]。

## 同步你的内容

当你的 Quartz 达到满意的状态时，可以把更改保存到 GitHub。
首先，确保你已经 [[installation#设置你的 GitHub 仓库|设置好 GitHub 仓库]]，然后运行 `npx quartz sync`。

## 自定义

对 `title`、`tags`、`aliases` 和 `cssclasses` 的 frontmatter 解析是 [[Frontmatter]] 插件的功能，`date` 由 [[CreatedModifiedDate]] 插件处理，`description` 由 [[Description]] 插件处理。自定义选项见各插件页面。
