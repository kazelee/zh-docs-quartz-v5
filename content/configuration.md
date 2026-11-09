---
date: 2026-09-20
title: 配置
---

Quartz 的设计目标是极高的可配置性，即使你完全不懂编程。你所需的大部分配置都可以通过编辑 `quartz.config.yaml` 完成。

> [!tip]
> 如果你使用像 VSCode 这样支持 YAML 的文本编辑器编辑 `quartz.config.yaml`，当配置出现错误时它会给出警告，帮助你避免配置失误！

Quartz 的配置可以分为两个主要部分：

```yaml title="quartz.config.yaml"
configuration:
  pageTitle: "My Site"
  # …… 通用配置
plugins:
  - source: github:quartz-community/some-plugin
    enabled: true
    # …… 插件条目
```

## 通用配置

这部分配置涉及任何会影响整个站点的内容。下面列出了你可以配置的所有项目：

- `pageTitle`：站点的标题。为站点生成 [[RSS Feed]] 时也会用到它。
- `pageTitleSuffix`：添加到页面标题末尾的字符串。它只作用于浏览器标签页标题，不影响页面顶部显示的标题。
- `enableSPA`：是否在站点上启用 [[SPA Routing]]。
- `enablePopovers`：是否在站点上启用 [[popover previews]]。
- `analytics`：站点使用的分析工具。取值可以是
  - `null`：不使用分析工具；
  - `{ provider: 'google', tagId: '<your-google-tag>' }`：使用 Google Analytics；
  - `{ provider: 'plausible' }`（托管版）或 `{ provider: 'plausible', host: 'https://<your-plausible-host>' }`（自托管，务必带上 `https://` 协议前缀）：使用 [Plausible](https://plausible.io/)；
  - `{ provider: 'umami', host: '<your-umami-host>', websiteId: '<your-umami-website-id>' }`：使用 [Umami](https://umami.is/)；
  - `{ provider: 'goatcounter', websiteId: 'my-goatcounter-id' }`（托管版）或 `{ provider: 'goatcounter', websiteId: 'my-goatcounter-id', host: 'my-goatcounter-domain.com', scriptSrc: 'https://my-url.to/counter.js' }`（自托管）使用 [GoatCounter](https://goatcounter.com)；
  - `{ provider: 'posthog', apiKey: '<your-posthog-project-apiKey>', host: '<your-posthog-host>' }`：使用 [Posthog](https://posthog.com/)；
  - `{ provider: 'tinylytics', siteId: '<your-site-id>' }`：使用 [Tinylytics](https://tinylytics.app/)；
  - `{ provider: 'cabin' }` 或 `{ provider: 'cabin', host: 'https://cabin.example.com' }`（自定义域名）：使用 [Cabin](https://withcabin.com)；
  - `{provider: 'clarity', projectId: '<your-clarity-id-code' }`：使用 [Microsoft clarity](https://clarity.microsoft.com/)。项目 ID 可以在概览页面顶部找到。
  - `{ provider: 'matomo', siteId: '<your-matomo-id-code', host: 'matomo.example.com' }`：使用 [Matomo](https://matomo.org/)，不带协议。
  - `{ provider: 'vercel' }`：使用 [Vercel Web Analytics](https://vercel.com/docs/concepts/analytics)。
  - `{ provider: 'rybbit', siteId: 'my-rybbit-id' }`（托管版）或 `{ provider: 'rybbit', siteId: 'my-rybbit-id', host: 'my-rybbit-domain.com' }`（自托管）使用 [Rybbit](https://rybbit.com)；
- `locale`：用于 [[i18n]] 和日期格式化
- `baseUrl`：用于生成站点地图和 RSS feed，它们需要绝对 URL 来了解站点的规范「主页」位于何处。这通常是你站点的部署 URL（例如本站的 `quartz.jzhao.xyz`）。不要包含协议（即 `https://`），也不要包含开头或结尾的斜杠。
  - 在 [[create|`npx quartz create`]] 期间会提示你设置它。CLI 会自动为你去掉 `https://` 或 `http://` 协议前缀以及结尾的斜杠。
  - 如果你在没有自定义域名的 GitHub pages 上托管（参见 [[hosting]]），这里还应包含子路径。例如，如果你的仓库是 `jackyzha0/quartz`，GitHub pages 会部署到 `https://jackyzha0.github.io/quartz`，此时 `baseUrl` 应为 `jackyzha0.github.io/quartz`。
  - 注意，Quartz 5 会尽可能避免使用它，并在可行时使用相对 URL，以确保无论你最终把站点实际部署到 _哪里_，站点都能正常工作。
- `ignorePatterns`：一组 [glob](<https://en.wikipedia.org/wiki/Glob_(programming)>) 模式，Quartz 在 `content` 文件夹中查找文件时会忽略这些模式、不进行搜索。详见 [[private pages]]。
- `theme`：配置站点的外观。
  - `fontOrigin`：从何处加载字体。
    - `"googleFonts"`（默认）：从 Google Fonts API 加载字体。这是最快的选项，尤其是在启用 CDN 缓存时。
    - `"local"`：下载字体并由你的站点提供。完全自包含，没有外部请求。
  - `cdnCaching`：如果为 `true`（默认），使用 Google CDN 缓存字体。这通常更快。如果你希望 Quartz 下载字体以实现自包含，请禁用（`false`）此项。
  - `typography`：使用哪些字体。[Google Fonts](https://fonts.google.com/) 上可用的任何字体都可以在这里使用。
    - `title`：站点标题使用的字体（可选，默认与 `header` 相同）
    - `header`：各级标题使用的字体
    - `code`：行内代码和代码块使用的字体
    - `body`：其余全部内容使用的字体
  - `colors`：控制站点的主题配色。
    - `light`：页面背景
    - `lightgray`：边框
    - `gray`：图谱连线、较粗的边框
    - `darkgray`：正文文字
    - `dark`：标题文字和图标
    - `secondary`：链接颜色、当前 [[graph view|关系图谱]] 节点
    - `tertiary`：悬停状态和已访问的 [[graph view|关系图谱]] 节点
    - `highlight`：内部链接背景、高亮文字、[[syntax highlighting|高亮的代码行]]
    - `textHighlight`：Markdown 高亮文字的背景

## 插件

你可以把 Quartz 插件看作对内容的一系列变换。

![[quartz transform pipeline.png]]

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/created-modified-date
    enabled: true
    order: 10 # 控制执行顺序
  - source: github:quartz-community/syntax-highlighting
    enabled: true
    order: 20
  # …… 更多插件
```

插件根据其 manifest 按类型（transformer、filter、emitter、pageType）分类。`order` 字段控制每个类别内的执行顺序。

> [!note]
> 如需通过 TS 对插件配置进行高级覆盖，你可以修改 `quartz.ts`：
>
> ```ts title="quartz.ts"
> import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"
>
> const config = await loadQuartzConfig({
>   // 在这里覆盖任意配置字段
> })
> export default config
> export const layout = await loadQuartzLayout()
> ```

- [[tags/plugin/transformer|转换器]] 对内容做 **map** 映射（例如解析 frontmatter、生成描述）
- [[tags/plugin/filter|过滤器]] 对内容进行 **filter** 过滤（例如过滤掉草稿）
- [[tags/plugin/emitter|输出器]] 对内容进行 **reduce** 归约（例如创建 RSS feed，或创建列出所有带有特定标签的文件的页面）
- **页面类型** 定义不同类型的页面如何渲染（内容页面、文件夹列表、标签列表）。每种页面类型可以使用不同的 [[layout#页面框架|页面框架]] 来控制其整体 HTML 结构。

`quartz.config.yaml` 中的 `layout.byPageType` 部分还可以设置 `template` 字段，为特定页面类型覆盖页面框架：

```yaml title="quartz.config.yaml"
layout:
  byPageType:
    canvas:
      template: minimal # 为 canvas 页面覆盖页面框架
```

可用的框架以及框架解析方式详见 [[layout#页面框架]]。

### 内部插件与外部插件

Quartz 区分两类插件：随 Quartz 一同打包的内部插件，以及单独安装的社区插件。

在 `quartz.config.yaml` 中，社区插件通过其 GitHub 源来引用：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
  - source: github:quartz-community/syntax-highlighting
    enabled: true
    options:
      theme:
        light: github-light
        dark: github-dark
```

内部插件（如 `FrontMatter`）随 Quartz 一同打包。社区插件单独安装，并通过其 `github:org/repo` 源引用。

### 社区插件

要安装社区插件，可以使用以下命令：

```shell
npx quartz plugin add github:quartz-community/explorer
```

这会将插件添加到 `quartz.config.yaml`，并安装到 `.quartz/plugins/`。

要安装配置中引用但尚未安装的所有插件（在克隆项目或设置 CI 时很有用）：

```shell
npx quartz plugin install --from-config
```

要移除已安装但不再出现在配置中的插件：

```shell
npx quartz plugin prune
```

这两个命令都支持 `--dry-run` 来预览变更。完整细节见 [[cli/plugin|插件 CLI 参考]]。

### 高级源选项

插件的 `source` 字段可以是简单字符串，也可以是带额外选项的对象。字符串形式最常见：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/explorer
    enabled: true
```

对于位于仓库子目录中的插件（monorepo 风格），或者当你需要固定到特定分支或标签时，请使用对象形式：

```yaml title="quartz.config.yaml"
plugins:
  - source:
      repo: "https://github.com/user/repo.git"
      subdir: plugin
      ref: main
      name: my-plugin
    enabled: true
```

对象形式支持以下字段：

| 字段     | 必填     | 说明                                                                                                      |
| -------- | :------: | --------------------------------------------------------------------------------------------------------- |
| `repo`   |    ✅    | Git 仓库 URL（例如 `https://github.com/user/repo.git`）。                                                 |
| `subdir` |    ❌    | 仓库中包含该插件的子目录。用于 monorepo 风格的插件仓库。                                                  |
| `ref`    |    ❌    | 要固定到的 Git ref（分支或标签）。等价于字符串源中的 `#ref` 后缀。                                        |
| `name`   |    ❌    | 覆盖在 `.quartz/plugins/` 中使用的目录名。默认为仓库名。                                                  |

> [!example] 真实示例
> [quartz-themes](https://github.com/saberzero1/quartz-themes) 插件位于其仓库的 `plugin/` 子目录中。要安装它：
>
> ```yaml title="quartz.config.yaml"
> plugins:
>   - source:
>       name: quartz-themes
>       repo: "https://github.com/saberzero1/quartz-themes.git"
>       subdir: plugin
>     enabled: true
>     options:
>       theme: "tokyo-night"
>       mode: both
> ```

> [!tip]
> 字符串形式 `github:user/repo#branch` 与对象形式 `{ repo, ref }` 是等价的指定分支方式。当你还需要 `subdir` 或 `name`，或者更喜欢可读性更强的配置时，请使用对象形式。

### 用法

你可以在 `quartz.config.yaml` 中添加、移除和重排插件，从而自定义 Quartz 的行为。每个插件条目都会指定其源、是否启用、执行顺序以及任何选项：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/note-properties
    enabled: true
    options:
      includeAll: false
      includedProperties:
        - description
        - tags
        - aliases
    order: 5
  - source: github:quartz-community/created-modified-date
    enabled: true
    options:
      priority:
        - frontmatter
        - git
        - filesystem
    order: 10
  - source: github:quartz-community/latex
    enabled: true
    options:
      renderEngine: katex
    order: 80
```

> [!note]
> 有些插件选项需要 JavaScript 回调函数（例如自定义排序、过滤或映射函数），这些无法用 YAML 表达。对于这些情况，请使用 `quartz.ts` 中的 TS 覆盖：
>
> ```ts title="quartz.ts"
> import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"
> import * as ExternalPlugin from "./.quartz/plugins"
>
> ExternalPlugin.Explorer({
>   mapFn: (node) => {
>     node.displayName = node.displayName.toUpperCase()
>     return node
>   },
> })
>
> const config = await loadQuartzConfig()
> export default config
> export const layout = await loadQuartzLayout()
> ```
>
> 在 `quartz.ts` 中设置的选项会与 YAML 选项合并，并具有更高优先级。插件覆盖必须放在 `loadQuartzConfig()` **之前**，这样在配置加载期间实例化组件时才会生效。可用的回调选项请参阅各插件的文档。

你可以在 [[tags/plugin|这里]] 查看所有插件及其配置选项的列表。

如果你想自己编写插件，请参阅 [[making plugins|制作自定义插件]] 指南。

## 字体

在 `quartz.config.yaml` 中，字体可以用简单字符串指定，也可以使用高级选项：

```yaml title="quartz.config.yaml"
configuration:
  theme:
    typography:
      title: Schibsted Grotesk # 可选，默认为 header 字体
      header: Schibsted Grotesk
      body: Source Sans Pro
      code: IBM Plex Mono
```

如需更精细地控制字重和斜体，请使用 `quartz.ts` 中的 TS 覆盖：

```ts title="quartz.ts"
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"

const config = await loadQuartzConfig({
  theme: {
    typography: {
      header: {
        name: "Schibsted Grotesk",
        weights: [400, 700],
        includeItalic: true,
      },
      body: "Source Sans Pro",
      code: "IBM Plex Mono",
    },
  },
})
export default config
export const layout = await loadQuartzLayout()
```

> [!tip]
> 如需按标题级别控制字体、自托管字体，或桥接 Obsidian 主题字体，请参阅 [[plugins/Fonts|Fonts]] 插件。它可以在构建时下载 Google Fonts，并通过 `fontOrigin: selfHosted` 在本地提供，使站点完全自包含。
