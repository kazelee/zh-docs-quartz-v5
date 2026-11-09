---
date: 2026-09-20
title: 引用 (Citations)
description: 通过 BibTeX 提供学术引用与参考文献支持。
tags:
  - plugin/transformer
image: https://images.unsplash.com/photo-1582079133805-43655f026448
repository: "[quartz-community/citations](https://github.com/quartz-community/citations)"
enabled: false
required: false
---

该插件为 Quartz 添加引用支持。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件接受以下配置选项：

- `bibliographyFile`：参考文献文件的路径。默认为 `./bibliography.bib`。该路径相对于你库的 git 源。
- `suppressBibliography`：是否抑制文档末尾的参考文献列表。默认为 `false`。
- `linkCitations`：是否将引用链接到参考文献列表。默认为 `false`。
- `csl`：要使用的引用样式。默认为 `apa`。更多选项请参考 [rehype-citation](https://rehype-citation.netlify.app/custom-csl)。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.Citations()`。
- 来源：[`quartz-community/citations`](https://github.com/quartz-community/citations)
- 安装：`npx quartz plugin add github:quartz-community/citations`
