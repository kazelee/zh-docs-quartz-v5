---
title: 文件夹与标签列表
tags:
  - feature/emitter
---

Quartz 会为你拥有的任何文件夹和标签输出列表页。

## 文件夹列表

Quartz 会为该文件夹下的所有页面生成一个索引页。这包括任意层级深度的内容。

此外，Quartz 也会为子文件夹生成页面。假设你在嵌套文件夹 `content/abc/def/note.md` 中有一个笔记，那么 Quartz 会为 `abc` 下的所有笔记生成一个页面，_并且_ 为 `abc/def` 下的所有笔记生成一个页面。

你可以通过引用文件夹名称并加上末尾斜杠来链接到文件夹列表，就像这样：`[[advanced/]]`（结果为 [[advanced/]]）。

默认情况下，Quartz 会将页面标题设为 `Folder: <folder name>`，并且没有描述。你可以通过在该文件夹中创建一个 `index.md` 文件并使用 `title` [[authoring content#Syntax|frontmatter]] 字段来覆盖它。你在这个文件中写入的任何内容也会被用作文件夹描述。

例如，对于文件夹 `content/posts`，你可以再添加一个文件 `content/posts/index.md` 来为它添加专门的描述。

## 标签列表

Quartz 还会为你的库中的每个唯一标签创建一个索引页，并渲染包含该标签的所有笔记的列表。

Quartz 同样支持标签层级（例如 `plugin/emitter`），并且会为标签层级的每一级分别渲染一个标签页。它还会在 `/tags` 处创建一个默认的全局标签索引页，用于显示你 Quartz 中所有标签的列表。

你可以通过使用 `tag/` 前缀引用标签名称来链接到标签列表，就像这样：`[[tags/plugin]]`（结果为 [[tags/plugin]]）。

与文件夹列表类似，你也可以通过为每个标签创建一个文件来为标签页提供描述和标题。例如，如果你想为 #component 标签创建自定义描述，需要在 `content/tags/component.md` 创建一个文件，并在其中写入标题和描述。

## 自定义

Quartz 允许你为这两种页面类型的内容定义自定义排序。文件夹列表是 [[FolderPage]] 插件的功能，标签列表是 [[TagPage]] 插件的功能。有关自定义选项，请参阅插件页面。
