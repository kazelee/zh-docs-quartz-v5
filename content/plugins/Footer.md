---
date: 2026-09-20
title: 页脚 (Footer)
description: 带可配置链接的页面页脚。
tags:
  - plugin/component
image:
repository: "[quartz-community/footer](https://github.com/quartz-community/footer)"
enabled: true
required: false
---

该插件在页面底部渲染一个页脚，其中包含「Created with Quartz」消息和一组可配置的链接。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 配置

该插件接受以下配置选项：

- `links`：要在页脚中显示的链接标签到其 URL 的映射。默认为 `{}`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/footer
  enabled: true
  options:
    links:
      GitHub: https://github.com/jackyzha0/quartz
      Discord Community: https://discord.gg/cRFFHYye7t
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Footer()`。
- 来源：[`quartz-community/footer`](https://github.com/quartz-community/footer)
- 安装：`npx quartz plugin add github:quartz-community/footer`
