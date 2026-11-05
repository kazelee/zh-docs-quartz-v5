---
title: 评论 (Comments)
description: 评论系统集成（Giscus、Utterances 等）。
tags:
  - plugin/component
image: "[[giscus-results.png]]"
repository: "[quartz-community/comments](https://github.com/quartz-community/comments)"
enabled: false
required: false
---

评论系统（giscus、utterances 等）。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

详细用法请参见 [[plugins/Comments]]。

## 配置

本插件接受以下配置选项：

- `provider`：要使用的评论提供方。目前仅支持 `giscus`。
- `options`：提供方专属选项。
  - `repo`：用于评论的 GitHub 仓库。
  - `repoId`：GitHub 仓库的 ID。
  - `category`：要使用的讨论分类。
  - `categoryId`：讨论分类的 ID。
  - `lang`：评论系统的语言。默认为 `en`。
  - `themeUrl`：包含自定义主题的文件夹的 URL。
  - `lightTheme`：浅色主题 CSS 文件的文件名。默认为 `light`。
  - `darkTheme`：深色主题 CSS 文件的文件名。默认为 `dark`。
  - `mapping`：如何将页面映射到讨论。默认为 `url`。
  - `strict`：使用严格的标题匹配。默认为 `true`。
  - `reactionsEnabled`：是否为帖子正文启用表情回应。默认为 `true`。
  - `inputPosition`：评论输入框相对于评论的位置。默认为 `bottom`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/comments
  enabled: true
  options:
    provider: giscus
    options:
      repo: jackyzha0/quartz
      repoId: MDEwOlJlcG9zaXRvcnkzODcyMTMyMDg
      category: Announcements
      categoryId: DIC_kwDOFxRnmM4B-Xg6
      lang: en
```

## API

- 类别：组件
- 函数名：`ExternalPlugin.Comments()`。
- 源码：[`quartz-community/comments`](https://github.com/quartz-community/comments)
- 安装：`npx quartz plugin add github:quartz-community/comments`
