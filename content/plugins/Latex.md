---
date: 2026-09-20
title: LaTeX (Latex)
description: 通过 KaTeX 或 Typst 渲染 LaTeX 数学表达式。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/latex](https://github.com/quartz-community/latex)"
enabled: true
required: false
---

本插件为 Quartz 添加 LaTeX 支持。更多信息请参阅 [[features/Latex|Latex]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

该插件接受以下配置选项：

- `renderEngine`：用于渲染 LaTeX 公式的引擎。可以是 `"katex"`（对应 [KaTeX](https://katex.org/)）、`"mathjax"`（对应 [MathJax](https://www.mathjax.org/) [SVG 渲染](https://docs.mathjax.org/en/latest/output/svg.html)）或 `"typst"`（对应 [Typst](https://typst.app/)，一种编写 LaTeX 公式的新方式）。默认值为 KaTeX。
- `customMacros`：适用于所有 LaTeX 块的自定义宏。其形式为键值对，键是新的命令名，值是宏的展开内容。例如：`{"\\R": "\\mathbb{R}"}`
- `katexOptions`：传递给 KaTeX 渲染器的额外选项。可用选项请参阅 [KaTeX 文档](https://katex.org/docs/options)。
- `mathJaxOptions`：传递给 MathJax 渲染器的额外选项。可用选项请参阅 [MathJax 文档](https://docs.mathjax.org/en/latest/options/)。
- `typstOptions`：传递给 Typst 渲染器的额外选项。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.Latex()`。
- 来源：[`quartz-community/latex`](https://github.com/quartz-community/latex)
- 安装：`npx quartz plugin add github:quartz-community/latex`
