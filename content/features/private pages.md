---
title: 私有页面
tags:
  - feature/filter
---

有些笔记你可能不希望作为网站发布。Quartz 通过两种机制支持这一点，二者可以结合使用：

## 过滤器插件

[[making plugins#Filters|过滤器插件]] 是根据特定条件过滤内容的插件。默认情况下，Quartz 使用 [[RemoveDrafts]] 插件，它会过滤掉 frontmatter 中带有 `draft: true` 的任何笔记。

如果你只想发布选定数量的笔记，可以改用 [[ExplicitPublish]]，它会过滤掉所有笔记，只保留 frontmatter 中带有 `publish: true` 的笔记。

> [!warning]
> 无论使用哪种过滤器插件，**所有非 Markdown 文件都会被输出，并在最终构建中公开可用。** 这包括图片、录音、PDF 等文件。

## `ignorePatterns`

这是 `quartz.config.yaml` 中主 [[configuration]] 下的一个字段，允许你指定一组模式，从而完全排除解析。任何有效的 [fast-glob](https://github.com/mrmlnc/fast-glob#pattern-syntax) 模式在这里都可用。

> [!note]
> Bash 的 glob 语法与 fast-glob 的语法略有不同，使用 Bash 的语法可能会导致意外结果。

常见示例包括：

- `some/folder`：排除 `some/folder` 整个文件夹
- `*.md`：排除所有扩展名为 `.md` 的文件
- `!(*.md)` 排除所有 _不_ 具有 `.md` 扩展名的文件。注意否定模式 _必须_ 把模式的其余部分括在括号中！
- `**/private`：排除任意嵌套层级中名为 `private` 的任何文件或文件夹

> [!warning]
> 通过插件或 `ignorePatterns` 模式将某些内容标记为私有，只会阻止页面被包含在最终构建的站点中。如果你的 GitHub 仓库是公开的，请务必在你的 Quartz 的 `.gitignore` 中为这些内容添加忽略规则。更多信息请参阅 `git` 的[文档](https://git-scm.com/docs/gitignore#_pattern_format)。
