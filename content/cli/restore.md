---
date: 2026-09-20
title: quartz restore
---

`restore` 命令是一种安全机制，允许你从本地缓存中恢复**内容文件夹**。该命令只影响你的 Markdown 文件，不会恢复插件或配置。

要将插件恢复到特定状态，请使用 [[cli/plugin|npx quartz plugin install]]。

## 何时使用

在以下情况下，你应该使用 `restore`：

- 某次 `quartz upgrade` 失败并损坏了你的内容。
- 你不小心删除了内容文件夹中的文件。
- 你遇到了复杂的合并冲突，并希望撤销。

## 工作原理

Quartz 会为你的内容文件夹维护一个隐藏缓存。每次运行某些命令时，Quartz 都会确保 Markdown 文件的备份存在。`restore` 命令只是将这些文件复制回你的主内容目录。

```shell
npx quartz restore
```

## 示例工作流

如果更新失败并使你的项目处于损坏状态：

1. **恢复**：运行 `npx quartz restore` 以找回你的内容。
2. **清理**：使用 Git 重置其他损坏的代码文件。
3. **重试**：再次尝试更新，或手动应用你需要的更改。
