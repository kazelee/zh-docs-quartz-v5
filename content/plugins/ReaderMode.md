---
date: 2026-09-20
title: 阅读模式 (ReaderMode)
description: 无干扰阅读模式的切换开关。
tags:
  - plugin/component
image:
new-in-v5: true
repository: "[quartz-community/reader-mode](https://github.com/quartz-community/reader-mode)"
enabled: true
required: false
---

无干扰阅读模式。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

详细用法信息请参阅 [[reader mode]]。

## 配置

该插件接受以下配置选项：

- `enabled`：是否启用阅读模式。默认值为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/reader-mode
  enabled: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.ReaderMode()`。
- 来源：[`quartz-community/reader-mode`](https://github.com/quartz-community/reader-mode)
- 安装：`npx quartz plugin add github:quartz-community/reader-mode`
