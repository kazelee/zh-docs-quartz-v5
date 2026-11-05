---
title: 堆叠页面 (StackedPages)
description: Andy Matuschak 风格的堆叠滑动面板。
tags:
  - plugin/component
image:
new-in-v5: true
repository: "[quartz-community/stacked-pages](https://github.com/quartz-community/stacked-pages)"
enabled: true
required: false
---

Andy Matuschak 风格的堆叠页面（滑动面板）。点击内部链接会在水平堆叠中并排打开页面，让你可以追溯自己在笔记中的路径。每个面板显示完整页面，可单独滚动或关闭。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

## 用法

启用后，点击页面上的任意内部链接会在右侧以新面板打开链接页面，而不是跳转离开。URL 会通过 `#stacked=slug1,slug2` 哈希更新，编码你当前的堆叠，因此你可以分享或收藏特定的页面路径。

在移动端（宽度低于 800px）默认禁用堆叠页面，因为水平平移在小屏幕上效果不佳。在移动端，链接按常规方式跳转。

### 交互

- **点击链接**：在右侧以新面板打开目标页面。如果已达到面板数量上限，最左侧的面板会被移除。
- **关闭面板**：点击面板标题栏中的 × 按钮将其从堆叠中移除。
- **折叠的竖条**：当面板超出视口时，较早的面板会折叠为显示页面标题的细长竖条。点击竖条可将该面板重新置于焦点。
- **浏览器后退/前进**：完整的堆叠状态存储在 URL 哈希中并与浏览器历史集成，因此后退/前进导航可按预期工作。

## 配置

本插件接受以下配置选项：

- `maxTabs`：一次可见的堆叠面板最大数量。默认为 `8`。
- `mobileBreakpoint`：视口宽度（像素），低于该宽度时禁用堆叠页面，链接按常规方式跳转。默认为 `800`。
- `showSpines`：当面板超出视口时是否显示折叠的竖条标题。默认为 `true`。
- `animateTransitions`：是否对面板的打开/关闭过渡使用动画。默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/stacked-pages
  enabled: true
  layout:
    position: afterBody
    priority: 50
    display: all
  options:
    maxTabs: 8
    mobileBreakpoint: 800
    showSpines: true
    animateTransitions: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.StackedPages()`。
- 来源：[`quartz-community/stacked-pages`](https://github.com/quartz-community/stacked-pages)
- 安装：`npx quartz plugin add github:quartz-community/stacked-pages`
