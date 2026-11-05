---
title: quartz create
---

`create` 命令初始化一个新的 Quartz 项目。它帮助你设置内容文件夹、选择配置模板、设置站点的 base URL，并配置 Quartz 应如何处理你的 Markdown 文件。

## 标志

| 标志          | 简写      | 说明                                                                  |
| ------------- | --------- | --------------------------------------------------------------------- |
| `--template`  | `-t`      | 配置模板（`default`、`obsidian`、`ttrpg` 或 `blog`）                  |
| `--directory` | `-d`      | Quartz 将被初始化到的目录                                             |
| `--source`    | `-s`      | 你的 Markdown 文件的源目录                                            |
| `--strategy`  | `-X`      | 如何处理源文件（`new`、`copy` 或 `symlink`）                          |
| `--links`     | `-l`      | 如何解析内部链接（`absolute`、`shortest` 或 `relative`）              |
| `--baseUrl`   | `-b`      | 你站点的 base URL（例如 `mysite.github.io/quartz`）                    |
| `--verbose`   | `-v`      | 启用详细的日志记录                                                    |

## 模板

运行 `quartz create` 时，你可以选择一个配置模板，它会针对你的使用场景预先配置 Quartz。所选模板始终会覆盖 `quartz.config.yaml`，即使该文件已存在。应用模板后，Quartz 会自动运行插件解析，以安装模板所需的插件并移除不再被引用的插件。

- **Default**：干净的 Quartz 配置，带有合理的默认值。最适合从零开始。
- **Obsidian**：针对 Obsidian 库优化，完整支持 Obsidian 风格 Markdown（wikilink、callout、mermaid 图表等）。会自动将链接解析设置为 `shortest` 并跳过链接解析提示。
- **TTRPG**：在 Obsidian 模板的基础上增加了 [Leaflet bases 插件](https://github.com/Requiae/quartz-leaflet-bases-plugin) 和 [ITS 主题](https://github.com/saberzero1/quartz-themes)（`its-theme.ttrpg-dnd`）。非常适合 D&D 和 TTRPG wiki。同样会跳过链接解析提示。
- **Blog**：以博客为中心的配置，启用了 [recent notes](https://github.com/quartz-community/recent-notes)（显示最近 5 篇带标签的文章）并通过 giscus 启用了 [comments](https://github.com/quartz-community/comments)。你需要用自己的 giscus 仓库信息填写 `quartz.config.yaml` 中的 `TODO:` 占位值。

## Base URL

在设置过程中，Quartz 会询问你站点的 base URL。这是你的站点将要部署到的 URL（例如 `mysite.github.io/quartz`）。

- **不要**包含协议（`https://`）—— 如果包含了，它会被自动移除。
- 末尾的斜杠也会被自动移除。
- 有关 `baseUrl` 如何使用的更多细节，见 [[configuration]]。

## 策略

运行 `quartz create` 时，你必须为内容选择一种策略：

- **new**：创建一个全新的空内容文件夹。如果你要从零开始一个新项目，请使用此项。
- **copy**：将源目录中的所有文件复制到 Quartz 内容文件夹中。对于已有的库，这是最安全的选项，因为它不会改动你的原始文件。
- **symlink**：创建从 Quartz 内容文件夹到源目录的符号链接。你在源目录（例如 Obsidian 中）所做的任何更改都会立即反映到 Quartz 中。

## 链接解析

Quartz 需要知道如何解释你 Markdown 文件中的内部链接：

- **shortest**：将链接解析为最接近的匹配文件名。这是 Obsidian 的默认值。
- **absolute**：相对于内容文件夹的根目录解析链接。
- **relative**：相对于当前文件所在的位置解析链接。

> [!note]
> 使用 **Obsidian** 或 **TTRPG** 模板时，链接解析会自动设置为 `shortest`，并跳过该提示。

## 交互式引导

如果在不带任何参数的情况下运行 `npx quartz create`，它会引导你完成交互式设置：

1. **选择模板**：选择一个配置模板（`Default`、`Obsidian`、`TTRPG` 或 `Blog`）。
2. **选择策略**：在 `new`、`copy` 或 `symlink` 之间选择。
3. **输入 base URL**：提供你的站点将要托管的 URL。
4. **选择链接解析**：选择链接的格式化方式（Obsidian 与 TTRPG 模板会跳过此项）。
5. **完成**：Quartz 将设置目录结构、创建你的配置，并自动安装模板中引用的所有插件。

## 示例：导入 Obsidian 库

要创建一个直接链接到现有 Obsidian 库的 Quartz 项目：

```shell
npx quartz create --template obsidian --strategy symlink --source ~/Documents/MyVault
```

该命令告诉 Quartz 使用 Obsidian 模板（具备完整的 OFM 支持与 shortest 链接解析），查看位于 `~/Documents/MyVault` 的库，并使用符号链接，以便更改保持同步。

## 示例：设置博客

要快速设置一个包含最近笔记和评论的博客：

```shell
npx quartz create --template blog --strategy new --baseUrl myblog.github.io
```

设置完成后，编辑 `quartz.config.yaml`，在 comments 插件的部分填入你的 giscus 仓库信息。
