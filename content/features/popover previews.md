---
date: 2026-09-20
title: 弹出预览
---

与 Wikipedia 类似，当你在 Quartz 中悬停到链接上时，会弹出一个页面预览，你可以滚动查看完整内容。指向标题的链接也会滚动弹出框，以将该特定标题显示在视野中。

默认情况下，由于 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 的限制，Quartz 只会获取你的库内页面的预览。它通过选择所有带有 `popover-hint` 类的 HTML 元素来实现这一点。对于大多数页面，这包括页面标题、字数与阅读时间等页面元数据、标签以及实际的页面内容。

在[[creating components|创建你自己的组件]]时，你可以包含这个 `popover-hint` 类，使其也包含在弹出框中。

与 Obsidian 类似，[[quartz-layout-desktop.png|使用维基链接引用的图片]]也可以作为弹出框查看。

## 配置

- 移除弹出框：将 `quartz.config.yaml` 中的 `enablePopovers` 字段设为 `false`。
- 样式：`quartz/components/styles/popover.scss`
- 脚本：`quartz/components/scripts/popover.inline.ts`
