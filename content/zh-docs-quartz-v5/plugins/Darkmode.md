---
title: 深色模式 (Darkmode)
description: 在浅色和深色主题之间切换。
tags:
  - plugin/component
image: "#0052cc"
repository: "[quartz-community/darkmode](https://github.com/quartz-community/darkmode)"
enabled: true
required: false
---

深色模式切换开关。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

详细用法请参见 [[plugins/Darkmode]]。

## 配置

本插件接受以下配置选项：

- `enabled`：是否启用深色模式切换开关。默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/darkmode
  enabled: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Darkmode()`。
- 源码：[`quartz-community/darkmode`](https://github.com/quartz-community/darkmode)
- 安装：`npx quartz plugin add github:quartz-community/darkmode`
