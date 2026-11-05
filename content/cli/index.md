---
title: CLI 参考
---

Quartz CLI 是与 Quartz 项目交互的主要方式。它提供了创建新项目、构建静态站点、与 GitHub 同步以及管理插件的命令。

你可以使用 `npx quartz` 运行 CLI。

## 快速参考

| 命令      | 说明                                                     | 示例                     |
| --------- | ------------------------------------------------------- | ------------------------ |
| `create`  | 通过选择模板初始化新的 Quartz 项目                       | `npx quartz create`      |
| `build`   | 生成静态 HTML 文件                                       | `npx quartz build`       |
| `sync`    | 与 GitHub 同步内容                                       | `npx quartz sync`        |
| `upgrade` | 将 Quartz 升级到最新版本（别名：`update`）               | `npx quartz upgrade`     |
| `plugin`  | 管理 Quartz 插件（install、add、remove 等）              | `npx quartz plugin list` |
| `tui`     | 启动交互式插件管理器                                     | `npx quartz tui`         |

## 命令

- [[create|create]]: 通过选择模板（default、obsidian、ttrpg、blog）并配置 base URL 来初始化一个新的 Quartz 项目。
- [[build|build]]: 将你的 Quartz 站点构建为静态 HTML。包含一个开发服务器。
- [[sync|sync]]: 在本地机器与 GitHub 之间推送和拉取更改。
- [[upgrade|upgrade]]: 将 Quartz 框架升级到最新版本。也可通过 `npx quartz update` 使用。
- [[restore|restore]]: 从本地缓存恢复你的内容文件夹。
- [[cli/plugin|plugin]]: 安装、添加、删除、清理和配置插件。使用带标志的 `plugin install` 进行 lockfile/配置同步、更新和检查。
- [[tui|tui]]: 使用终端界面管理插件和布局。

## 全局标志

所有 Quartz 命令都接受这些标志：

| 标志            | 简写      | 说明                                                                                                    | 默认值         |
| --------------- | --------- | ------------------------------------------------------------------------------------------------------- | -------------- |
| `--directory`   | `-d`      | 包含你的 Quartz 项目的目录                                                                              | `content`      |
| `--verbose`     | `-v`      | 启用详细的日志记录以便调试                                                                              | `false`        |
| `--concurrency` | `-c`      | 并行执行的操作（例如 `build`、`plugin install`、`plugin add`）的最大并行工作线程数                      | CPU 核心数     |

不进行并行工作的命令会将 `-c` 视为空操作，因此始终可以安全地传入。实际示例见 [[build#Performance Tuning|build]] 和 [[cli/plugin#Installing on low-end hardware|plugin]]。

## 帮助与版本

要查看任何命令的完整可用标志列表，请使用 `--help` 标志。

```shell
npx quartz --help
npx quartz build --help
```

要检查你当前正在运行的 Quartz 版本，请使用 `--version` 标志。

```shell
npx quartz --version
```
