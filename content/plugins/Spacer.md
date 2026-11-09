---
date: 2026-09-20
title: 间隔 (Spacer)
description: 用于布局组的弹性间隔。
tags:
  - plugin/component
image:
new-in-v5: true
repository: "[quartz-community/spacer](https://github.com/quartz-community/spacer)"
enabled: true
required: false
---

本插件渲染一个弹性间隔元素，用于在布局组内将相邻组件推开。它使用 CSS `flex: 2 1 auto` 填充可用空间，因此适合在工具栏或侧边栏中分隔各项（例如，将左侧边栏工具栏中的搜索栏与深色模式切换按钮分开）。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

本插件没有配置选项。

## API

- 类别：组件
- 函数名：`ExternalPlugin.Spacer()`。
- 来源：[`quartz-community/spacer`](https://github.com/quartz-community/spacer)
- 安装：`npx quartz plugin add github:quartz-community/spacer`
