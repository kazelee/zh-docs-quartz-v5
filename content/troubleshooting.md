---
date: 2026-09-20
title: 故障排查
---

使用 Quartz 时的常见问题与解决方案。

## 构建错误

### `Could not resolve ...` 或模块缺失错误

这通常意味着某个插件未安装。请运行：

```bash
npx quartz plugin install
```

这会从 `quartz.lock.json` 把所有插件恢复到 `.quartz/plugins/`。

### 更新后出现 `tsc` 类型错误

运行 `npx quartz upgrade` 后，如果更新改变了你的 `quartz.ts` 覆盖项所依赖的内部 API，就可能出现类型错误。请查看更新日志中的破坏性变更，并相应更新你的覆盖项。

### 构建速度慢

尝试提高并发数：

```bash
npx quartz build --concurrency 8
# 或者使用简写：
npx quartz build -c 8
```

默认会使用所有可用的 CPU 核心。如果你处于内存受限的环境（CI），降低并发数反而可能有帮助。

## 插件问题

### 安装后插件未加载

1. 确认该插件出现在 `quartz.config.yaml` 的 `plugins:` 下
2. 检查是否设置了 `enabled: true`
3. 运行 `npx quartz plugin list` 确认它已安装
4. 运行 `npx quartz plugin install --latest --dry-run` 检查插件健康状况

### 插件选项未生效

确保你的 YAML 缩进正确。选项必须嵌套在插件条目之下：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/some-plugin
    enabled: true
    options:
      myOption: value # 正确：嵌套在 options 下
```

一个常见的错误是把选项放在了错误的缩进层级。

### `ExternalPlugin.X is not a function`

这意味着插件在 `quartz.ts` 中被引用但未安装。可以：

- 安装它：`npx quartz plugin add github:quartz-community/plugin-name`
- 或者从 `quartz.ts` 中移除该引用

### 全新克隆后插件构建失败

> [!important]
> 现在大多数社区插件都附带预构建的 `dist/` 目录，完全跳过构建步骤。下面描述的构建失败情况主要适用于仍在开发中的插件，或尚未采用预构建分发的旧插件。

在全新的克隆仓库中，`npx quartz plugin install`（或由 `npx quartz create` 自动执行的插件步骤）可能会报告少数插件构建失败——通常是 10–15 个左右。git clone 和 checkout 仍然成功，但插件内部的 `npm run build` 会报错。

出现这种情况是因为 `quartz.lock.json` 将每个插件固定到特定提交，而那些较旧的插件提交可能是针对更早版本的 `@quartz-community/types` / `@quartz-community/utils` 编写的，这些依赖的 git 仓库已不再提供当年发布的产物文件。于是插件的 `tsup`/`tsc` 构建无法解析预期的类型声明。

解决办法是把所有插件刷新到其默认分支上的最新提交：

```bash
npx quartz plugin install --latest
```

这会用最新的提交重写 `quartz.lock.json`（这些提交又固定了更新的 `@quartz-community/*` 版本，其构建产物是可获取的），并从头重新构建每个插件。完成这一步后，后续的 `npx quartz plugin install` 调用就能从刷新后的锁文件干净地恢复。

### `plugin install` 在低端硬件上卡住、OOM 或失败

> [!note]
> 预构建插件要快得多，也更省资源，因为它们跳过了 `npm install` 和 `npm run build` 步骤。

默认情况下，`npx quartz plugin install` 会在你所有的 CPU 核心上并行克隆、拉取和构建插件。每个并行工作进程都可能运行自己的 `npm install` 和 `npm run build`，非常消耗内存。在低端笔记本、Raspberry Pi、小型 VPS 实例或受限的 CI runner 上，这可能耗尽内存、触发 OOM killer，或让系统看起来卡住。

使用 `--concurrency` / `-c` 降低并行度：

```bash
# 一次安装一个插件（最安全，最慢）
npx quartz plugin install --latest -c 1

# 一次两个 —— 在 4 GB 内存的机器上通常可行
npx quartz plugin install --latest --concurrency 2
```

同一个标志也适用于 `plugin add` 以及已弃用的别名（`plugin update`、`plugin restore`、`plugin check`、`plugin resolve`）：

```bash
npx quartz plugin add github:quartz-community/some-plugin -c 1
```

如果使用 `-c 1` 时 `plugin install` 总是在同一个插件附近失败，问题很可能出在那个特定插件的构建上，而不是并发——可以尝试加上 `--verbose` 获取详细错误输出，并查看该插件自己的仓库中是否有已知问题。

## 内容问题

### 笔记没有显示

- 检查文件是否在 `content/` 文件夹中
- 检查 frontmatter 中是否设置了 `draft: true`（[[RemoveDrafts]] 插件会把这些过滤掉）
- 如果使用 [[ExplicitPublish]]，确保 frontmatter 中设置了 `publish: true`
- 检查[[configuration]]中的 `ignorePatterns`，确保该文件路径未被排除

### 维基链接无法解析

- 确保已启用 [[ObsidianFlavoredMarkdown]] 插件
- 确认目标笔记存在于你的内容文件夹中
- 检查文件名是否存在大小写问题

### 图片不显示

- 确保图片位于 Quartz 会处理的文件夹中（通常是 `content/` 或其子文件夹）
- 检查 Markdown 中的图片路径是否与实际文件位置一致
- [[Assets]] 输出器必须启用（默认已启用）

## GitHub 同步问题

### `fatal: --[no-]autostash option is only valid with --rebase`

你的 `git` 版本可能过旧。更新 git 即可解决。

### `fatal: The remote end hung up unexpectedly`

这通常是因为 Git 的默认缓冲区大小对你的内容来说太小了。请调大它：

```bash
git config http.postBuffer 524288000
```

### 同步时出现合并冲突

如果 `npx quartz sync` 遇到合并冲突：

1. 在你的编辑器中解决冲突
2. 运行 `git add .` 和 `git commit` 完成合并
3. 运行 `npx quartz sync --no-pull` 进行推送

如果你想重新开始，运行 `npx quartz restore` 从缓存恢复你的内容。

## 开发服务器问题

### 热重载不工作

- 确保你使用的是 `--serve` 模式：`npx quartz build --serve`
- 检查 3001 端口（WebSocket）是否被阻止——这是用于热重载通知的默认 `--wsPort`
- 如果在远程开发，使用 `--remoteDevHost` 设置正确的 WebSocket URL

### 端口已被占用

更换端口：

```bash
npx quartz build --serve --port 3000
```

## 还是卡住了？

- 查看 [GitHub Issues](https://github.com/jackyzha0/quartz/issues) 中是否有类似问题
- 在 [Discord 社区](https://discord.gg/cRFFHYye7t)提问
- 在命令中加上 `--verbose` 以获得更详细的错误输出
