---
title: 搜索 (Search)
description: 支持标签过滤和键盘导航的全文搜索。
tags:
  - plugin/component
image: https://images.unsplash.com/photo-1516382799247-87df95d790b7
repository: "[quartz-community/search](https://github.com/quartz-community/search)"
enabled: true
required: false
---

全文搜索功能。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

详细用法信息请参阅 [[full-text search]]。

## 配置

本插件接受以下配置选项：

- `enablePreview`：是否在搜索结果中显示页面内容预览。默认为 `true`。
- `fieldPriority`：指定搜索字段优先级顺序的数组。默认为 `["title", "content", "tags"]`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/search
  enabled: true
  options:
    enablePreview: true
    fieldPriority:
      - title
      - content
      - tags
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Search()`。
- 来源：[`quartz-community/search`](https://github.com/quartz-community/search)
- 安装：`npx quartz plugin add github:quartz-community/search`
