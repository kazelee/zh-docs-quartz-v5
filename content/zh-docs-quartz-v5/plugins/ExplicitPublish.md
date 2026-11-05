---
title: 显式发布 (ExplicitPublish)
description: "仅发布显式标记为 publish: true 的页面。"
tags:
  - plugin/filter
image:
repository: "[quartz-community/explicit-publish](https://github.com/quartz-community/explicit-publish)"
enabled: false
required: false
---

该插件根据 frontmatter 中显式的 `publish` 标志过滤内容，只允许显式标记为待发布的内容通过。它是 [[RemoveDrafts]] 的主动选择版本。更多信息请参阅 [[private pages]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件没有配置选项。

## API

- 类别：过滤器
- 函数名：`ExternalPlugin.ExplicitPublish()`。
- 来源：[`quartz-community/explicit-publish`](https://github.com/quartz-community/explicit-publish)
- 安装：`npx quartz plugin add github:quartz-community/explicit-publish`
