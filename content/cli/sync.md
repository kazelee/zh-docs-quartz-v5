---
date: 2026-09-20
title: quartz sync
---

`sync` 命令自动化了将本地更改推送到 GitHub 以及从远程仓库拉取更新的过程。它为希望保持站点更新而不手动运行 Git 命令的用户简化了 Git 工作流。

## 标志

| 标志          | 简写      | 说明                                 | 默认值             |
| ------------- | --------- | ------------------------------------ | ------------------ |
| `--directory` | `-d`      | 你的 Quartz 项目所在的目录           | 当前目录           |
| `--verbose`   | `-v`      | 启用详细的日志记录                   | `false`            |
| `--commit`    |           | 是否提交更改                         | `true`             |
| `--no-commit` |           | 跳过提交更改                         | `false`            |
| `--message`   | `-m`      | 自定义提交信息                       | `update content`   |
| `--push`      |           | 是否将更改推送到远程                 | `true`             |
| `--no-push`   |           | 跳过推送更改                         | `false`            |
| `--pull`      |           | 是否从远程拉取更改                   | `true`             |
| `--no-pull`   |           | 跳过拉取更改                         | `false`            |

## 工作流

当你运行 `npx quartz sync` 时，Quartz 会执行以下步骤：

1. **拉取**：从远程 GitHub 仓库获取并合并更改。
2. **添加**：暂存项目中的所有新文件和已修改文件。
3. **提交**：用你的更改创建一个新的提交。
4. **推送**：将你的新提交发送到 GitHub。

## 常见工作流

### 常规同步

最常见的用法是不带任何标志直接运行该命令。它会拉取、提交并推送所有内容。

```shell
npx quartz sync
```

### 首次同步

如果你刚刚新建了仓库并且还没有推送任何内容，可能需要跳过拉取步骤。

```shell
npx quartz sync --no-pull
```

### 自定义提交信息

你可以为更改提供一个更具描述性的信息。

```shell
npx quartz sync --message "add new notes about gardening"
```

### 从另一台设备同步

如果你在另一台电脑上工作，只想获取最新的更改而暂时不推送任何内容。

```shell
npx quartz sync --no-push --no-commit
```

## 故障排除

### Git 缓冲区

如果你的更改数量非常多，Git 有时可能因缓冲区限制而失败。如果发生这种情况，请尝试分批同步较少的文件，或增大 Git post buffer 的大小。

### Autostash

Quartz 内部使用 `git pull --rebase --autostash`。这意味着如果你在运行 `sync` 时有未暂存的更改，Quartz 会临时隐藏它们、拉取远程更改，然后恢复你的更改。如果此过程中发生冲突，你需要使用标准 Git 工具手动解决。

有关初始设置的更多信息，见 [[installation]]。
