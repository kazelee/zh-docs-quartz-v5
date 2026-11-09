---
date: 2026-09-20
title: 关系图谱 (Graph)
description: 交互式链接关系图谱可视化。
tags:
  - plugin/component
image:
repository: "[quartz-community/graph](https://github.com/quartz-community/graph)"
enabled: true
required: false
---

交互式关系图谱可视化。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

详细用法信息请参阅 [[graph view]]。

## 配置

该插件接受以下配置选项：

- `localGraph`：局部关系图谱视图的选项。
- `globalGraph`：全局关系图谱视图的选项。

`localGraph` 和 `globalGraph` 都接受以下选项：

- `drag`：是否允许拖动节点。默认为 `true`。
- `zoom`：是否允许缩放。默认为 `true`。
- `depth`：关系图谱的深度。局部默认为 `1`，全局默认为 `-1`。
- `scale`：关系图谱的初始缩放比例。局部默认为 `1.1`，全局默认为 `0.9`。
- `repelForce`：将节点相互推开的力。默认为 `0.5`。
- `centerForce`：将节点拉向中心的力。局部默认为 `0.3`，全局默认为 `0.2`。
- `linkDistance`：相连节点之间的距离。默认为 `30`。
- `fontSize`：节点标签的字体大小。默认为 `0.6`。
- `opacityScale`：节点不透明度的缩放比例。默认为 `1`。
- `removeTags`：要从关系图谱中排除的标签。默认为 `[]`。
- `showTags`：是否在关系图谱中显示标签。默认为 `true`。
- `enableRadial`：是否启用径向布局。局部默认为 `false`，全局默认为 `true`。
- `focusOnHover`：是否聚焦于悬停的节点。局部默认为 `false`，全局默认为 `true`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/graph
  enabled: true
  options:
    localGraph:
      drag: true
      zoom: true
      depth: 1
      scale: 1.1
      repelForce: 0.5
      centerForce: 0.3
      linkDistance: 30
      fontSize: 0.6
      opacityScale: 1
      removeTags: []
      showTags: true
      focusOnHover: false
      enableRadial: false
    globalGraph:
      drag: true
      zoom: true
      depth: -1
      scale: 0.9
      repelForce: 0.5
      centerForce: 0.3
      linkDistance: 30
      fontSize: 0.6
      opacityScale: 1
      removeTags: []
      showTags: true
      focusOnHover: true
      enableRadial: true
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Graph()`。
- 来源：[`quartz-community/graph`](https://github.com/quartz-community/graph)
- 安装：`npx quartz plugin add github:quartz-community/graph`
