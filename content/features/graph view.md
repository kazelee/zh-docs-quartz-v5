---
date: 2026-09-20
title: "关系图谱"
tags:
  - component
---

Quartz 具有一个关系图谱功能，既能显示局部关系图谱，也能显示全局关系图谱。

- 局部关系图谱显示那些链接到当前文件的文件，或被当前文件链接的文件。换言之，它显示所有 _至多_ 相隔一跳的笔记。
- 全局关系图谱可以通过点击局部关系图谱右上角的图谱图标来切换。它显示图谱中的 _所有_ 笔记，以及它们之间如何连接。

> [!info]
> 关系图谱现在是社区插件。这展示了外部插件如何扩展 Quartz 的功能，同时可作为插件开发者的参考实现。

## 安装

关系图谱可以从 GitHub 作为社区插件获取：

```bash
npm install github:quartz-community/graph --legacy-peer-deps
```

然后将其添加到你的 `quartz.config.yaml`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/graph
    enabled: true
    layout:
      position: right
      priority: 10
```

## 特性

默认情况下，节点半径与该文件的内链和外链总数成正比。

此外，与浏览器用不同颜色高亮已访问链接类似，关系图谱也会用不同颜色显示你已访问过的节点。

> [!info]
> 关系图谱要求 [[configuration]] 中存在 `ContentIndex` 输出器插件。

## 自定义

大多数配置可以通过向 `Graph()` 传入选项来完成。

例如，默认配置如下所示：

```yaml title="quartz.config.yaml"
plugins:
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
    layout:
      position: right
      priority: 10
```

当你传入自己的选项时，如果想为某个字段保留默认值，可以省略该字段或全部字段。

想要进一步自定义？

- 移除关系图谱：从 `quartz.config.yaml` 中移除 `graph` 条目，或设置 `enabled: false`
- 组件源码：https://github.com/quartz-community/graph
