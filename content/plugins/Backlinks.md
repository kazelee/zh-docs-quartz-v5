---
date: 2026-09-20
title: 反向链接 (Backlinks)
description: 显示链接到当前页面的页面。
tags:
  - plugin/component
image:
repository: "[quartz-community/backlinks](https://github.com/quartz-community/backlinks)"
enabled: true
required: false
---

显示链接到当前页面的页面。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

详细用法信息请参阅 [[plugins/Backlinks]]。

## 配置

该插件接受以下配置选项：

- `hideWhenEmpty`：如果当前页面没有反向链接，则隐藏反向链接区域。默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/backlinks
  enabled: true
  options:
    hideWhenEmpty: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Backlinks()`。
- 来源：[`quartz-community/backlinks`](https://github.com/quartz-community/backlinks)
- 安装：`npx quartz plugin add github:quartz-community/backlinks`
