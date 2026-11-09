---
date: 2026-09-20
title: 维基链接
---

维基链接（wikilinks）由早期互联网 wiki 首创，目的是让你无需每次都编写 Markdown 或 HTML 链接，就能更轻松地在页面之间编写链接。

Quartz 默认支持维基链接，这些链接由 Quartz 使用 [[CrawlLinks]] 插件解析。有关维基链接语法的更多信息，请参阅 [Obsidian 关于内部链接的帮助页面](https://help.obsidian.md/Linking+notes+and+files/Internal+links)。

此功能作为 [[Obsidian compatibility]] 的一部分启用，可以在该插件中配置和启用/禁用。

维基链接匹配不区分大小写，以与 Obsidian 保持一致：`[[My Note]]`、`[[my note]]` 和 `[[MY NOTE]]` 都解析到同一个文件。生成的 URL 为小写（例如 `my-note`）。

## 语法

- `[[Path to file]]`：生成指向 `Path to file.md`（或 `Path-to-file.md`）的链接，文本为 `Path to file`
- `[[Path to file | Here's the title override]]`：生成指向 `Path to file.md` 的链接，文本为 `Here's the title override`
- `[[Path to file#Anchor]]`：生成指向文件 `Path to file.md` 中锚点 `Anchor` 的链接
- `[[Path to file#^block-ref]]`：生成指向文件 `Path to file.md` 中特定块 `block-ref` 的链接

### 嵌入

- `![[Path to image]]`：将图片嵌入页面
- `![[Path to image|100x145]]`：以 100px × 145px 的尺寸将图片嵌入页面
- `![[Path to file]]`：嵌入整个页面
- `![[Path to file#Anchor]]`：嵌入标题 `Anchor` 下的所有内容
- `![[Path to file#^b15695]]`：嵌入 ID 为 `^b15695` 的块
