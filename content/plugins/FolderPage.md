---
date: 2026-09-20
title: 文件夹页面 (FolderPage)
description: 为文件夹生成列表页面。
tags:
  - plugin/pageType
image:
repository: "[quartz-community/folder-page](https://github.com/quartz-community/folder-page)"
enabled: true
required: false
---

该插件是一个页面类型插件，为文件夹生成索引页面，为每个包含多个内容文件的文件夹创建一个列表页面。它使用 `default` 的 [[layout#页面框架|页面框架]]（带侧边栏的三栏布局）。更多信息请参阅 [[folder and tag listings]]。

示例：[[advanced/|进阶]]

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件接受以下配置选项：

- `showFolderCount`：是否显示文件夹中的页面数量。默认为 `true`。
- `showSubfolders`：是否在列表中包含来自子文件夹的页面。默认为 `true`。
- `sort`：类型为 `(f1: QuartzPluginData, f2: QuartzPluginData) => number{:ts}` 的函数，用于排序条目。默认按日期排序，并在日期相同时按字典序决定顺序。需要通过 TS 覆盖设置。
- `prefixFolders`：若为 `true`，生成的文件夹页面标题会加上前缀 "Folder: "（例如 "Folder: notes"）。默认为 `false`。

## API

- 类别：页面类型
- 函数名：`ExternalPlugin.FolderPage()`。
- 来源：[`quartz-community/folder-page`](https://github.com/quartz-community/folder-page)
- 安装：`npx quartz plugin add github:quartz-community/folder-page`
