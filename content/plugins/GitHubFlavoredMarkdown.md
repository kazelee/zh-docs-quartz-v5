---
title: GitHub 风格 Markdown (GitHubFlavoredMarkdown)
description: GitHub 风格 Markdown 支持（表格、任务列表、脚注、删除线）。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/github-flavored-markdown](https://github.com/quartz-community/github-flavored-markdown)"
enabled: true
required: false
---

该插件增强了 Markdown 处理能力，以支持 GitHub Flavored Markdown（GFM），后者增加了自动链接字面量、脚注、删除线、表格和任务列表等特性。

此外，该插件还提供可选的排版优化特性（例如把直引号转换为弯引号、把短横线转换为连接号/长破折号，以及省略号），以及自动生成的标题链接——它表现为悬停在标题旁时出现的一个符号。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件接受以下配置选项：

- `enableSmartyPants`：为 true 时启用排版增强。默认为 true。
- `linkHeadings`：为 true 时自动为标题添加链接。默认为 true。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.GitHubFlavoredMarkdown()`。
- 来源：[`quartz-community/github-flavored-markdown`](https://github.com/quartz-community/github-flavored-markdown)
- 安装：`npx quartz plugin add github:quartz-community/github-flavored-markdown`
