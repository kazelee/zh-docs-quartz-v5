---
title: 标注块（callout）
tags:
  - feature/transformer
---

Quartz 支持与 Obsidian 相同的 Admonition 标注块（callout）语法。

这包括

- 12 种不同的标注块类型（每种都有若干别名）
- 可折叠的标注块

```
> [!info] Title
> This is a callout!
```

参见[此处关于受支持类型与语法的文档](https://help.obsidian.md/Editing+and+formatting/Callouts)。

> [!warning]
> 想知道为什么即使启用了标注块，它们也可能不显示？你可能需要调整插件顺序，使 [[ObsidianFlavoredMarkdown]] 位于 [[SyntaxHighlighting]] _之后_。

## 自定义

标注块（callout）是 [[ObsidianFlavoredMarkdown]] 插件的一项功能。有关如何启用或禁用它们，请参阅该插件页面。

你可以通过自定义 `quartz/styles/callouts.scss` 来修改图标。

### 添加自定义标注块

默认情况下，自定义标注块会应用 `note` 样式来处理。要做出更花哨的效果，你需要将以下代码行添加到 `custom.scss` 中。

```scss title="quartz/styles/custom.scss"
.callout {
  &[data-callout="custom"] {
    --color: #customcolor;
    --border: #custombordercolor;
    --bg: #custombg;
    --callout-icon: url("data:image/svg+xml; utf8, <custom formatted svg>"); //SVG 图标代码
  }
}
```

> [!warning]
> 别忘了在把 SVG 放进 CSS 之前确保它已经过 URL 编码。你可以使用[这个工具](https://yoksel.github.io/url-encoder/)来帮你完成。

## 展示

> [!info]
> 默认标题

> [!question]+ 标注块可以 _嵌套_ 吗？
>
> > [!todo]- 可以！而且还能折叠！
> >
> > > [!example] 你甚至可以使用多层嵌套。

> [!note]
> 别名："note"

> [!abstract]
> 别名："abstract"、"summary"、"tldr"

> [!info]
> 别名："info"

> [!todo]
> 别名："todo"

> [!tip]
> 别名："tip"、"hint"、"important"

> [!success]
> 别名："success"、"check"、"done"

> [!question]
> 别名："question"、"help"、"faq"

> [!warning]
> 别名："warning"、"attention"、"caution"

> [!failure]
> 别名："failure"、"missing"、"fail"

> [!danger]
> 别名："danger"、"error"

> [!bug]
> 别名："bug"

> [!example]
> 别名："example"

> [!quote]
> 别名："quote"、"cite"
