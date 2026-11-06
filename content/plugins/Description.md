---
title: 描述 (Description)
description: 为元数据和预览生成页面描述。
tags:
  - plugin/transformer
image:
repository: "[quartz-community/description](https://github.com/quartz-community/description)"
enabled: true
required: false
---

本插件生成描述，这些描述用作 HTML `head` 的元数据、[[RSS Feed]] 的元数据；在 [[folder and tag listings]] 中，如果没有主体正文内容，描述会被用作标题与列表之间的文字。

如果 frontmatter 包含 `description` 属性，则使用该属性（参见 [[authoring content#Syntax]]）。否则，插件会尽可能使用内容的前几句话来达到目标描述长度。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

本插件接受以下配置选项：

- `descriptionLength`：生成描述的目标长度。默认为 150 个字符。截断发生在超过给定长度后结束的第一个 _句子_ 之后。
- `maxDescriptionLength`：描述的最大硬性长度。如果生成的描述超过此长度，则用省略号截断。默认为 300 个字符。
- `replaceExternalLinks`：若为 `true`（默认值），则在描述中把外部链接替换为其域名和路径（例如 `https://domain.tld/some_page/another_page?query=hello&target=world` 会被替换为 `domain.tld/some_page/another_page`）。

## API

- 类别：转换器
- 函数名：`ExternalPlugin.Description()`。
- 源码：[`quartz-community/description`](https://github.com/quartz-community/description)
- 安装：`npx quartz plugin add github:quartz-community/description`
