---
title: frontmatter 解析 (Frontmatter)
aliases:
  - note-properties
  - Note Properties
description: 解析 frontmatter，并在可折叠面板中显示笔记属性。
tags:
  - plugin/transformer
  - plugin/component
publish: true
enableToc: true
image:
repository: "[quartz-community/note-properties](https://github.com/quartz-community/note-properties)"
enabled: true
required: true
---

该插件使用 [gray-matter](https://github.com/jonschlinkert/gray-matter) 库解析页面的 frontmatter，并可选择在可折叠面板中显示选定的属性。更多信息请参阅 [[authoring content#Syntax]]、[[Obsidian compatibility]] 和 [[OxHugo compatibility]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

> [!warning]
> 不得移除此插件，否则 Quartz 会出问题。

## 配置

该插件接受以下配置选项：

- `delimiters`：用于 frontmatter 的分隔符。可以是一个值（例如 `"---"`），也可以是分别用于开始和结束分隔符的两个值（例如 `["---", "~~~"]`）。默认为 `"---"`。
- `language`：用于解析 frontmatter 的语言。可以是 `yaml`（默认）或 `toml`。
- `includeAll`：在属性面板中包含所有 frontmatter 属性。当为 `false` 时，只显示 `includedProperties` 中的属性。默认为 `false`。
- `includedProperties`：当 `includeAll` 为 `false` 时要包含的属性。默认为 `["description", "tags", "aliases"]`。
- `excludedProperties`：始终排除在显示之外的属性，即使 `includeAll` 为 `true` 也是如此。默认为 `[]`。
- `hidePropertiesView`：隐藏可视化属性面板，但仍处理 frontmatter。如果你只需要解析 frontmatter 而不需要界面，这会很有用。默认为 `false`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/note-properties
  enabled: true
  options:
    includeAll: false
    includedProperties:
      - description
      - tags
      - aliases
    excludedProperties: []
    hidePropertiesView: false
    delimiters: "---"
    language: yaml
```

## 属性面板

启用后，该插件会在页面正文之前渲染一个可折叠的「Properties」面板。该面板以表格形式显示选定的 frontmatter 字段，并自动按类型渲染：

- **字符串**以纯文本显示。字符串中的 [[wikilinks]] 和 [Markdown 链接](https://example.com) 会渲染为可点击的链接。维基链接目标会像正文内容链接一样被 slug 化（例如 `[[My Note]]` 解析为 `my-note`），并且匹配时不区分大小写，以与 Obsidian 的行为保持一致，因此 `[[MyNote]]`、`[[mynote]]` 和 `[[MYNOTE]]` 都指向同一个页面。
- **数组**渲染为逗号分隔的列表。
- **布尔值**渲染为禁用的复选框。
- **数字**使用等宽字体渲染。
- **对象**以 JSON 形式在代码块中渲染。
- **标签**得到特殊处理：它们渲染为高亮链接，指向对应的标签页面。
- **Null/undefined** 值显示为长破折号（—）。

### 按笔记覆盖

你可以使用 frontmatter 键按笔记控制属性面板：

- `quartz-properties`（或 `quartzProperties`）：设为 `true` 强制显示面板，或设为 `false` 强制隐藏面板，覆盖全局的 `hidePropertiesView` 设置。
- `quartz-properties-collapse`（或 `quartzPropertiesCollapse`）：设为 `true` 使面板初始为折叠状态，或设为 `false` 使面板初始为展开状态，覆盖默认的折叠状态。

这些键会自动从可见的属性表格中排除。

```yaml title="Example frontmatter"
---
title: My Note
quartz-properties: true
quartz-properties-collapse: false
---
```

## 支持的 frontmatter

Quartz 支持以下 frontmatter 字段。当列出多个键时，它们是别名——会使用第一个匹配的键。

| 字段 | 键 | 说明 |
| --- | --- | --- |
| 标题 | `title` | 页面标题。为空时回退到文件名。 |
| 描述 | `description` | 用于元数据和搜索的页面描述。 |
| 标签 | `tags`, `tag` | 分类标签。像文件路径一样被 slug 化：空格变为 `-`，`&` 变为 `-and-`，`%` 变为 `-percent`，标签会转为小写，因此 `#MyTag` 和 `#mytag` 解析到同一个标签页面（与 Obsidian 一致）。 |
| 别名 | `aliases`, `alias` | 页面的替代名称，用于链接解析。 |
| 永久链接 | `permalink` | 自定义 URL slug。也会被加入别名。 |
| CSS 类 | `cssclasses`, `cssclass` | 应用于页面正文的 CSS 类。 |
| 社交图片 | `socialImage`, `image`, `cover` | 用于社交媒体预览的图片。 |
| 社交描述 | `socialDescription` | 专门用于社交媒体预览的描述。 |
| 创建日期 | `created`, `date` | 笔记的创建时间。 |
| 修改日期 | `modified`, `lastmod`, `updated`, `last-modified` | 笔记的最后修改时间。未设置时回退到 `created`。 |
| 发布日期 | `published`, `publishDate`, `date` | 笔记的发布时间。 |
| 发布 | `publish` | 笔记是否应被发布。 |
| 草稿 | `draft` | 笔记是否为草稿。 |
| 评论 | `comments` | 是否为笔记启用评论。 |
| 语言 | `lang` | 笔记的语言代码。 |
| 启用目录 | `enableToc` | 是否显示目录。 |

## API

- 类别：转换器、组件
- 函数名：`ExternalPlugin.NoteProperties()`。
- 来源：[`quartz-community/note-properties`](https://github.com/quartz-community/note-properties)
- 安装：`npx quartz plugin add github:quartz-community/note-properties`
