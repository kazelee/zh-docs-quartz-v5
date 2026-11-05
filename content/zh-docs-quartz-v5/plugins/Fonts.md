---
title: 字体 (Fonts)
description: 细粒度的字体控制，支持按标题级别设置、Google Fonts 集成以及主题字体发现。
tags:
  - plugin/transformer
  - plugin/emitter
image:
repository: "[quartz-community/fonts](https://github.com/quartz-community/fonts)"
enabled: true
required: false
---

该插件为你的 Quartz 站点提供细粒度的字体控制。它支持为不同标题级别设置字体族、在与 [Quartz Themes](https://github.com/saberzero1/quartz-themes) 配合使用时自动发现主题字体、集成 Google Fonts 并自动加载字重与斜体，并且在无法确定时回退到 Obsidian 的默认系统字体栈。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

## 为什么使用 Fonts？

Quartz 使用三个 CSS 变量设置字体：`--headerFont`、`--bodyFont` 和 `--codeFont`。Obsidian 主题使用另一套系统，包括按标题级别区分的变量（`--h1-font` 到 `--h6-font`）、`--font-text` 和 `--font-monospace`。这两套系统无法正确衔接，导致标题字体无法按主题的预期渲染。

Fonts 通过以下方式解决这个问题：

1. 衔接 Obsidian 与 Quartz 的字体系统
2. 输出**未分层**的 CSS，从而正确覆盖 Quartz 的基础标题样式
3. 提供两套系统单独都无法提供的按标题级别字体控制
4. 可选地从 Google Fonts 加载字体，并细粒度控制字重与斜体

## 配置

字体选项可以接受 CSS font-family 字符串，或一个带 Google Fonts 加载控制的对象：

```yaml
# 字符串形式
body: '"Inter", sans-serif'

# 对象形式（用于控制 Google Fonts 的字重/斜体）
body:
  name: Inter
  weights: [400, 600, 700]
  includeItalic: true
```

该插件接受以下配置选项：

| 选项 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `title` | `FontSpecification` | `header` value | 站点标题的字体族。 |
| `body` | `FontSpecification` | Obsidian default | 正文文本的字体族。 |
| `header` | `FontSpecification` | Obsidian default | 所有标题（h1-h6）的默认字体族。 |
| `code` | `FontSpecification` | Obsidian default | 代码与等宽元素的字体族。 |
| `interface` | `FontSpecification` | Obsidian default | UI 元素的字体族。 |
| `h1` – `h6` | `FontSpecification` | `header` value | 按标题级别的字体族覆盖。 |
| `useThemeFonts` | `boolean` | `true` | 当 [Quartz Themes](https://github.com/saberzero1/quartz-themes) 已安装时，使用其中的字体作为默认值。 |
| `fontOrigin` | `string` | `"googleFonts"` | `"googleFonts"` 表示从 Google Fonts CDN 加载，`"selfHosted"` 表示下载并在本地提供，`"local"` 表示不加载。 |

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/fonts
  enabled: true
  options:
    useThemeFonts: true
    fontOrigin: googleFonts
```

### 字体解析

字体按优先级链解析：

```
User config (plugin options)
  → Theme fonts (from Quartz Themes, if installed)
    → Obsidian defaults (system font stacks)
```

对于单个标题：

```
h1 option → header option → theme --h1-font → theme font → Obsidian default
```

对于站点标题：

```
title option → header option → theme font → Obsidian default
```

## 与 Quartz Themes 配合使用

当 [Quartz Themes](https://github.com/saberzero1/quartz-themes) 安装并启用后，Fonts 会自动发现主题的字体元数据，并将其用作默认值。你在 Fonts 中显式设置的任何选项都会覆盖主题字体。

Fonts 必须在 Quartz Themes **之后**运行。插件排序会自动处理这一点（Quartz Themes = 10，Fonts = 60）。

> [!warning]
> 如果 Quartz Themes 已启用，但在 Fonts 执行时尚未运行，你会在控制台中看到一条警告。请确保 Quartz Themes 的 `defaultOrder` 小于 Fonts。

## 不配合 Quartz Themes 使用

Fonts 可以独立工作。在没有主题的情况下，它会回退到 Obsidian 的默认系统字体栈。你可以通过插件选项显式设置字体。

## 示例

```yaml title="quartz.config.yaml"
# 自动使用主题字体（默认行为）
- source: github:quartz-community/fonts
  enabled: true

# 只覆盖标题字体
- source: github:quartz-community/fonts
  enabled: true
  options:
    header: '"Playfair Display", serif'

# 完全控制：按标题级别设置字体
- source: github:quartz-community/fonts
  enabled: true
  options:
    body: '"Inter", sans-serif'
    header: '"Playfair Display", serif'
    code: '"JetBrains Mono", monospace'
    h1: '"Playfair Display", serif'
    h2: '"Lora", serif'

# 自动从 Google Fonts 加载
- source: github:quartz-community/fonts
  enabled: true
  options:
    fontOrigin: googleFonts
    body: Inter
    header: Playfair Display
    code: JetBrains Mono

# 带字重/斜体控制的 Google Fonts
- source: github:quartz-community/fonts
  enabled: true
  options:
    fontOrigin: googleFonts
    body:
      name: Inter
      weights: [400, 600, 700]
      includeItalic: true
    header:
      name: Playfair Display
      weights: [400, 700]
    code:
      name: JetBrains Mono
      weights: [400]

# 自定义标题字体（与 header 分开）
- source: github:quartz-community/fonts
  enabled: true
  options:
    fontOrigin: googleFonts
    title: Abril Fatface
    header: Playfair Display
    body: Inter
    code: JetBrains Mono

# 自托管字体（构建时下载，无外部请求）
- source: github:quartz-community/fonts
  enabled: true
  options:
    fontOrigin: selfHosted
    body: Inter
    header: Playfair Display
    code: JetBrains Mono

# 完全忽略主题字体
- source: github:quartz-community/fonts
  enabled: true
  options:
    useThemeFonts: false
    body: '"Inter", sans-serif'
```

## 自托管字体

当设置了 `fontOrigin: selfHosted` 时，Fonts 会在构建期间从 Google Fonts 下载字体，并从你站点的 `static/fonts/` 目录提供这些字体。这使你的站点完全自包含，运行时不向 Google 发起任何外部请求。

在构建时，插件会：

1. 获取你配置的字体的 Google Fonts CSS
2. 下载每个字体文件（`.woff2`、`.woff` 等）
3. 将字体文件写入构建输出中的 `static/fonts/`
4. 生成 `quartz-fonts.css` 文件，其中包含指向本地文件的 `@font-face` 规则

> [!note]
> 自托管字体需要在 Quartz 配置中设置 `baseUrl`，因为 CSS 中的字体 URL 需要绝对路径。

```yaml title="quartz.config.yaml"
configuration:
  baseUrl: "example.com"

plugins:
  - source: github:quartz-community/fonts
    enabled: true
    options:
      fontOrigin: selfHosted
      body: Inter
      header: Playfair Display
      code: JetBrains Mono
```

## Google Fonts 校验

当设置了 `fontOrigin: googleFonts` 且安装了可选的 [`google-font-metadata`](https://www.npmjs.com/package/google-font-metadata) 包时，Fonts 会在构建时校验你的字体配置：

- 检查字体族名称是否存在于 Google Fonts 中。
- 如果请求的字重对该字体不可用，则发出警告。
- 如果请求了斜体但字体不支持，则发出警告。

安装它以启用校验：

```bash
npm install google-font-metadata
```

校验警告会记录到控制台，但不会阻止构建。

## API

- 类别：转换器、输出器
- 函数名：`ExternalPlugin.Fonts()`（转换器）、`ExternalPlugin.FontsEmitter()`（输出器）。
- 来源：[`quartz-community/fonts`](https://github.com/quartz-community/fonts)
- 安装：`npx quartz plugin add github:quartz-community/fonts`
