---
title: 别名重定向 (AliasRedirects)
description: 根据 frontmatter 别名和保留大小写的 URL 生成重定向页面。
tags:
  - plugin/emitter
image: https://images.unsplash.com/photo-1601735479770-bb5de9dbe844
repository: "[quartz-community/alias-redirects](https://github.com/quartz-community/alias-redirects)"
enabled: true
required: false
---

该插件输出 HTML 重定向页面，使旧 URL 重定向到规范页面。它处理两类重定向：

1. **frontmatter 别名**：为内容的 frontmatter 中定义的别名生成重定向页面。
2. **保留大小写的重定向**：为因 Quartz v5 的小写 slug 规范化而发生变化的 URL 自动生成重定向页面。

### Frontmatter 别名

如果 `foo.md` 具有以下 frontmatter：

```md title="foo.md"
---
title: "Foo"
aliases:
  - "bar"
---
```

目标 `host.me/bar` 将被永久重定向到 `host.me/foo`。

该输出器支持以下 frontmatter 字段：

- `aliases`
- `alias`

### 保留大小写的重定向

Quartz v5 会将所有 URL 规范化为小写。如果你从 v4（保留原始大小写）迁移，之前已被索引的包含大写字母的 URL（例如 `/Diary/My-Note`）会返回 404 错误。

当启用 `enableCaseRedirects`（默认启用）时，该插件会自动检测原始路径与小写 slug 不同的文件，并在原始大小写的 URL 处生成重定向页面。例如，如果你的内容目录包含 `Diary/2026-01-01.md`，插件会生成：

- `/diary/2026-01-01` 处的规范页面（由常规构建生成）
- `/Diary/2026-01-01` 处的重定向页面（由该插件生成）

重定向页面包含适当的 SEO 信号：

- `<meta http-equiv="refresh" content="0; url=...">`，用于立即重定向
- `<link rel="canonical">`，指向小写 URL
- `<meta name="robots" content="noindex">`，用于防止重复索引

这样可以保留搜索引擎排名，并确保入站链接继续有效。

> [!note]
> 保留大小写的重定向在对大小写不敏感的文件系统（macOS、Windows）上没有效果，因为服务器已经会将任一种大小写解析到同一个文件。该插件会自动检测文件系统类型，并在不需要时跳过重定向生成。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件接受以下配置选项：

- `enableCaseRedirects`：如果为 `true`（默认），则为因 v5 的小写规范化而改变大小写的 URL 自动生成重定向页面。设为 `false` 可禁用此行为。

## API

- 类别：输出器
- 函数名：`ExternalPlugin.AliasRedirects()`。
- 来源：[`quartz-community/alias-redirects`](https://github.com/quartz-community/alias-redirects)
- 安装：`npx quartz plugin add github:quartz-community/alias-redirects`
