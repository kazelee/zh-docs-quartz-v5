---
date: 2026-09-20
title: 移除草稿 (RemoveDrafts)
description: 过滤掉标记为草稿的页面。
tags:
  - plugin/filter
image:
repository: "[quartz-community/remove-draft](https://github.com/quartz-community/remove-draft)"
enabled: true
required: false
---

该插件会过滤掉你库中的内容，以便只让已完成的内容对外提供。这可以防止 [[private pages]] 被发布。默认情况下，它会过滤掉 frontmatter 中带有 `draft: true` 的所有页面，其余页面保持原样。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件没有配置选项。

## API

- 类别：过滤器
- 函数名：`ExternalPlugin.RemoveDrafts()`。
- 来源：[`quartz-community/remove-draft`](https://github.com/quartz-community/remove-draft)
- 安装：`npx quartz plugin add github:quartz-community/remove-draft`
