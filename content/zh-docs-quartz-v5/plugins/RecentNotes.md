---
title: 最近笔记 (RecentNotes)
description: 显示最近修改的笔记列表。
tags:
  - plugin/component
image:
repository: "[quartz-community/recent-notes](https://github.com/quartz-community/recent-notes)"
enabled: false
required: false
---

显示最近修改的笔记。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

详细用法信息请参阅 [[recent notes]]。

## 配置

该插件接受以下配置选项：

- `title`：最近笔记区域的标题。默认值为 `Recent notes`。
- `limit`：最多显示的最近笔记数量。默认值为 `3`。
- `showTags`：是否显示每条笔记的标签。默认值为 `true`。
- `linkToMore`：指向展示更多笔记的页面的 slug。默认值为 `false`。
- `hideTagPages`：是否在列表中隐藏标签索引页。默认值为 `false`。
- `hideFolderPages`：是否在列表中隐藏文件夹索引页。默认值为 `false`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/recent-notes
  enabled: true
  options:
    limit: 3
    showTags: true
    hideTagPages: false
    hideFolderPages: false
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.RecentNotes()`。
- 来源：[`quartz-community/recent-notes`](https://github.com/quartz-community/recent-notes)
- 安装：`npx quartz plugin add github:quartz-community/recent-notes`
