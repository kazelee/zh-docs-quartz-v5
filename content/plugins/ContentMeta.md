---
date: 2026-09-20
title: 内容元数据 (ContentMeta)
description: 显示创建日期和阅读时间。
tags:
  - plugin/component
image:
repository: "[quartz-community/content-meta](https://github.com/quartz-community/content-meta)"
enabled: true
required: false
---

本插件在文章标题下方显示内容元数据，例如创建日期和预计阅读时间。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

## 配置

本插件接受以下配置选项：

- `showReadingTime`：是否显示预计阅读时间。默认为 `true`。
- `showComma`：是否在元数据项之间显示逗号。默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/content-meta
  enabled: true
  options:
    showReadingTime: true
    showComma: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.ContentMeta()`。
- 源码：[`quartz-community/content-meta`](https://github.com/quartz-community/content-meta)
- 安装：`npx quartz plugin add github:quartz-community/content-meta`
