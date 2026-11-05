---
title: quartz plugin
---

`plugin` 命令是 Quartz v5 插件管理系统的核心。它允许你直接在命令行中安装、配置和更新插件。

所有插件都存储在 `.quartz/plugins/` 目录中，其版本记录在 `quartz.lock.json` 中。

## 子命令

### list

列出当前已安装的所有插件及其版本。

```shell
npx quartz plugin list
```

### add

从 Git 仓库添加新插件。

```shell
npx quartz plugin add github:username/repo
```

要从特定分支或 ref 安装，请在源后面附加 `#ref`：

```shell
npx quartz plugin add github:username/repo#my-branch
npx quartz plugin add git+https://github.com/username/repo.git#my-branch
npx quartz plugin add https://github.com/username/repo.git#my-branch
```

你也可以从本地目录添加插件。这适用于本地开发或离线环境：

```shell
npx quartz plugin add ./path/to/my-plugin
npx quartz plugin add ../sibling-plugin
npx quartz plugin add /absolute/path/to/plugin
```

本地插件会以符号链接的形式放入 `.quartz/plugins/`，因此你对源目录所做的任何更改都会立即反映，无需重新安装。

指定分支后，该分支会存储在 lockfile 中。之后的所有命令（`install`、`prune`）都会自动遵循该分支。使用 `install --latest` 可获取该分支的最新提交。

> [!tip]
> `plugin add` 也接受 `--concurrency` / `-c`，用于限制同时克隆和构建的远程仓库数量。这与 [[#install]] 下记录的标志相同，在低端硬件上一次添加多个插件时很有用。

### remove

移除已安装的插件。

```shell
npx quartz plugin remove plugin-name
```

### install

为你的 Quartz 项目安装插件。默认情况下，这会安装 `quartz.lock.json` 文件中列出的所有插件。

```shell
npx quartz plugin install
```

#### 标志

- `--from-config`：与 `quartz.config.yaml` 同步插件，而不是与 lockfile 同步。这会安装缺失的插件并清理孤立的插件。
- `--latest`：从插件的远程源获取最新版本，而不是使用 lockfile 中的版本。
- `--clean`：跳过已存在的目录，执行全新的安装。
- `--dry-run`：预览更改，但不实际安装或移除任何文件。
- `--concurrency`、`-c`：并行克隆、获取和构建的最大插件数。默认为 CPU 核心数。在内存或 CPU 受限、默认并行度会导致失败、OOM 或卡死的机器上，请调低此值（例如 `-c 1` 或 `-c 2`）。见下文的 [[#Installing on low-end hardware]]。

#### 位置参数

- `[names..]`：可选，指定要安装或更新的插件名列表。

```shell
# 将指定插件更新到最新版本
npx quartz plugin install --latest plugin-a plugin-b

# 预览将从配置中安装的内容
npx quartz plugin install --from-config --dry-run
```

### enable / disable

在不移除插件文件的情况下，切换插件在 `quartz.config.yaml` 中的状态。

```shell
npx quartz plugin enable plugin-name
npx quartz plugin disable plugin-name
```

### config

查看或修改特定插件的配置。

```shell
# 查看配置
npx quartz plugin config plugin-name

# 设置一个值
npx quartz plugin config plugin-name --set key=value
```

### prune

移除在 `quartz.config.yaml` 中不再被引用的已安装插件。这在你从配置中删除插件条目后进行清理时很有用。

> [!note]
> 运行 `plugin install --from-config` 也会在同步过程中移除孤立插件。当你只想清理而不安装任何新内容时，请使用 `prune`。

```shell
npx quartz plugin prune
```

使用 `--dry-run` 可以在不做任何更改的情况下预览哪些插件会被移除：

```shell
npx quartz plugin prune --dry-run
```

## 常见工作流

### 添加并启用插件

要添加新插件并开始使用它：

1. 添加插件：`npx quartz plugin add github:quartz-community/example`
2. 启用插件：`npx quartz plugin enable example`

### 更新所有内容

要让你的插件保持最新：

```shell
npx quartz plugin install --latest
```

### 在低端硬件上安装

默认情况下，`plugin install` 和 `plugin add` 会在你的所有 CPU 核心上并行克隆、获取和构建插件。在内存受限的机器上（低端笔记本、Raspberry Pi、小型 VPS 实例、受限的 CI 运行器），这可能耗尽内存或使系统不堪重负，因为每个工作线程可能同时启动自己的 `npm install` / `npm run build`。

> [!note]
> 现在大多数社区插件都带有预构建的 `dist/` 目录。当 Quartz 发现该目录时，会完全跳过安装和构建步骤，使过程更快、占用资源更少。本节主要适用于开发中的插件或未提供预构建分发的插件。

如果你的机器上 `plugin install` 失败、卡死或 OOM，请使用 `--concurrency` / `-c` 降低并发度：

```shell
# 一次安装一个插件（最安全，最慢）
npx quartz plugin install --latest -c 1

# 一次两个 —— 在 4 GB 机器上通常是很好的平衡
npx quartz plugin install --latest --concurrency 2
```

该标志在 `plugin add` 以及其他执行并行工作的插件子命令上同样有效：

```shell
npx quartz plugin add github:quartz-community/some-plugin -c 1
```

### 管理配置

如果你想在不打开 YAML 文件的情况下更改某个插件设置：

```shell
npx quartz plugin config explorer --set useSavedState=true
```

### 清理未使用的插件

如果你已从配置中移除插件并想清理遗留文件：

```shell
npx quartz plugin prune --dry-run  # 先预览
npx quartz plugin prune            # 移除孤立插件
```

### 从配置进行设置

在新机器或 CI 中设置时，`install --from-config` 会确保已安装的插件与你的配置一致 —— 安装缺失的插件，并移除不再被引用的插件：

```shell
npx quartz plugin install --from-config
```

### 使用分支进行测试

如果插件作者在单独的分支上提供了修复或功能，你可以直接安装，而无需等待其发布到默认分支：

```shell
# 从功能分支安装
npx quartz plugin add github:username/repo#fix/some-bug

# 之后，通过不带 ref 重新添加来切回默认分支
npx quartz plugin remove repo
npx quartz plugin add github:username/repo
```

分支 ref 会被记录在 `quartz.lock.json` 中，因此在插件被不带 ref 重新添加之前，`install --latest` 会继续遵循指定的分支。

如果不存在 `quartz.config.yaml`，`prune` 和 `install --from-config` 都会回退到 `quartz.config.default.yaml`。

### 本地插件开发

对于本地插件开发或离线环境，你可以从本地目录添加插件：

```shell
npx quartz plugin add ./my-local-plugin
```

本地插件会以符号链接的形式放入 `.quartz/plugins/`，因此更改会立即反映。当你运行 `install --latest` 时，本地插件会被重新构建（npm install + npm run build），不涉及任何 git 操作。

> [!note]
> 本地符号链接插件通常使用这种安装时构建的兜底方式，因为 `dist/` 目录在开发期间通常被 git 忽略。

`install --latest --dry-run` 命令会将本地插件显示为「local」状态，而不是检查远程更新。

要将本地插件切换回 git 源：

```shell
npx quartz plugin remove my-local-plugin
npx quartz plugin add github:username/my-local-plugin
```

### 子目录（Monorepo）插件

有些插件位于较大仓库的子目录中，而不是根目录。对于这些插件，你可以在 `quartz.config.yaml` 中将插件源指定为一个带有 `subdir` 字段的对象：

```yaml title="quartz.config.yaml"
plugins:
  - source:
      repo: "https://github.com/username/monorepo.git"
      subdir: plugin
    enabled: true
```

这会告诉 Quartz 克隆整个仓库，但只安装指定子目录的内容。

你可以将 `subdir` 与 `ref` 组合使用以固定分支或标签，并用 `name` 覆盖插件目录名：

```yaml title="quartz.config.yaml"
plugins:
  - source:
      repo: "https://github.com/username/monorepo.git"
      subdir: packages/my-plugin
      ref: v2.0
      name: my-plugin
    enabled: true
```

有关对象源字段的完整参考，见 [[configuration#Advanced Source Options|高级源选项]]。

> [!note]
> `plugin add` CLI 命令只支持字符串形式的源。要使用带 `subdir` 的对象源格式，请直接编辑 `quartz.config.yaml`，然后运行 `npx quartz plugin install --from-config` 进行安装。

## 从已弃用的命令迁移

| 旧命令                                | 新等价命令                                          |
| ------------------------------------- | --------------------------------------------------- |
| `npx quartz plugin restore`           | `npx quartz plugin install --clean`                 |
| `npx quartz plugin update`            | `npx quartz plugin install --latest`                |
| `npx quartz plugin update my-plugin`  | `npx quartz plugin install --latest my-plugin`      |
| `npx quartz plugin check`             | `npx quartz plugin install --latest --dry-run`      |
| `npx quartz plugin resolve`           | `npx quartz plugin install --from-config`           |
| `npx quartz plugin resolve --dry-run` | `npx quartz plugin install --from-config --dry-run` |
| `npx quartz update`                   | `npx quartz plugin install --latest`                |

这些旧命令仍可作为隐藏别名使用，但会打印弃用警告。

## 插件状态

不带任何子命令运行 plugin 命令时，会显示所有已安装插件的状态面板，包括是否有可用更新：

```shell
npx quartz plugin
```

它会显示每个插件的源、提交、启用/禁用状态，并并行检查可用的更新。如需完整的交互式管理界面，请改用 [[tui|npx quartz tui]]。
