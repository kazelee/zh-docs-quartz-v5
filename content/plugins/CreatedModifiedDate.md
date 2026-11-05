---
title: 创建与修改日期 (CreatedModifiedDate)
description: 从 frontmatter、git 或文件系统确定创建日期和修改日期。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/created-modified-date](https://github.com/quartz-community/created-modified-date)"
enabled: true
required: false
---

本插件使用三种可能的数据源为文档确定创建日期、修改日期和发布日期：frontmatter 元数据、Git 历史记录和文件系统。更多信息请参见 [[authoring content#Syntax]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

本插件接受以下配置选项：

- `priority`：用于查询日期信息的数据源。优先级最高的在前。可选值为 `"frontmatter"`、`"git"` 和 `"filesystem"`。默认为 `["frontmatter", "git", "filesystem"]`。
- `defaultDateType`：显示日期时使用哪种日期类型。可以是 `"created"`、`"modified"` 或 `"published"`。默认为 `"modified"`。

加载 frontmatter 时，会使用 [[Frontmatter#List]] 的值。

> [!warning]
> 如果你依赖 `git` 获取日期，请确保在插件选项中把 `defaultDateType` 设为 `modified`。
>
> 取决于你 [[hosting|托管]] Quartz 的方式，本地文件的 `filesystem` 日期可能与最终日期不一致。在这些情况下，最好使用 `git` 或 `frontmatter` 来保证日期正确。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.CreatedModifiedDate()`。
- 源码：[`quartz-community/created-modified-date`](https://github.com/quartz-community/created-modified-date)
- 安装：`npx quartz plugin add github:quartz-community/created-modified-date`
