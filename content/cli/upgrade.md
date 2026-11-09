---
date: 2026-09-20
title: quartz upgrade
---

`upgrade` 命令通过从官方 Quartz 仓库拉取更改，将 Quartz 框架本身升级到最新版本。

## 用法

```shell
npx quartz upgrade
```

## 工作原理

当你运行 `npx quartz upgrade` 时，Quartz 会执行以下步骤：

1. **备份你的内容** —— 你的内容文件夹会缓存在本地，以防数据丢失。
2. **拉取最新的 Quartz 代码** —— 使用 Git 从官方上游仓库（`upstream/v5`）获取并合并。
3. **显示版本变化** —— 展示版本过渡（例如 `v5.0.0 → v5.1.0`），或确认你已经是最新版本。
4. **更新依赖** —— 运行 `npm install`，确保所有包与新版本匹配。
5. **恢复插件** —— 从 `quartz.lock.json` 重新安装插件，以确保兼容性。
6. **检查插件兼容性** —— 验证已安装的插件与新 Quartz 版本兼容。

## 处理冲突

由于 Quartz 允许你自定义几乎每一部分代码，升级有时会导致合并冲突。如果你修改了 Quartz 团队也更新过的文件，就会出现这种情况。

Quartz 通过在拉取前备份你的 lockfile 并在之后恢复它，自动处理 `quartz.lock.json` 中的合并冲突。这可以避免升级过程中最常见的冲突来源。

对于其他文件，如果发生冲突：

1. Git 会在受影响的文件中标记冲突部分。
2. 你需要打开这些文件，手动选择要保留哪些更改。
3. 解决冲突后，你可以提交更改。

## 恢复

如果升级出现问题或使你的项目处于不可用状态，你可以使用 [[restore|restore]] 命令从本地缓存恢复你的内容。

## 标志

`upgrade` 命令支持标准的 [[cli/index|通用标志]]（`--directory`、`--verbose`）。

## 另见

- [[cli/plugin|quartz plugin install --latest]] —— 更新已安装的插件
- [[upgrading|升级 Quartz]] —— 详细的升级指南
- [[restore|quartz restore]] —— 从缓存恢复内容
