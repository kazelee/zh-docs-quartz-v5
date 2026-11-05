---
title: 阅读模式
tags:
  - component
---

阅读模式（Reader Mode）是一项通过隐藏侧边栏和其他 UI 元素来让你专注于内容的功能。启用后，它提供干净、无干扰的阅读体验。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

## 配置

阅读模式默认启用。要禁用它，请在 `quartz.config.yaml` 中设置 `enabled: false`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/reader-mode
    enabled: false
```

或者完全移除该插件：

```bash
npx quartz plugin remove github:quartz-community/reader-mode
```

- 安装：`npx quartz plugin add github:quartz-community/reader-mode`
- 源码：[`quartz-community/reader-mode`](https://github.com/quartz-community/reader-mode)

## 使用

阅读模式切换按钮显示为一个带有书本图标的按钮。点击后：

- 侧边栏被隐藏
- 将鼠标悬停在内容区域上会临时显示侧边栏

与深色模式不同，阅读模式的状态不会在页面重新加载之间保留，但会在站点内的 SPA 导航期间维持。

## 自定义

你可以通过 CSS 变量和样式自定义阅读模式的外观。该组件使用以下类：

- `.readermode`：切换按钮
- `.readerIcon`：书本图标
- `[reader-mode="on"]`：阅读模式激活时应用于根元素

在自定义 CSS 中的自定义示例：

```scss
.readermode {
  // 自定义按钮
  svg {
    stroke: var(--custom-color);
  }
}
```
