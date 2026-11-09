---
date: 2026-09-20
title: 反向链接
tags:
  - component
---

笔记的反向链接是指从其他笔记指向该笔记的链接。如果你启用了 [[popover previews]] 功能，反向链接面板中的链接也会显示丰富的预览。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 自定义

- 移除反向链接：从 `quartz.config.yaml` 中删除 `backlinks` 条目，或设置 `enabled: false`。
- 空时隐藏：如果给定页面不包含任何反向链接，则隐藏 `Backlinks`（默认为 `true`）。要禁用此行为，请在 `quartz.config.yaml` 的插件选项中设置 `hideWhenEmpty: false`。
- 安装：`npx quartz plugin add github:quartz-community/backlinks`
- 源码：[`quartz-community/backlinks`](https://github.com/quartz-community/backlinks)
