---
title: 制作你自己的插件
---

> [!warning]
> 这部分文档假定你具备 TypeScript 的实际使用知识，并会包含描述 Quartz 插件接口应是什么样子的代码片段。

Quartz 的插件是对内容的一系列变换。下面的处理流水线图展示了这一点：

![[quartz transform pipeline.png]]

所有插件都定义为一个函数：它接收一个用于选项的单一参数 `type OptionType = object | undefined`，并返回一个与其插件类型相对应的对象。

```ts
type OptionType = object | undefined
type QuartzPlugin<Options extends OptionType = undefined> = (opts?: Options) => QuartzPluginInstance
type QuartzPluginInstance =
  | QuartzTransformerPluginInstance
  | QuartzFilterPluginInstance
  | QuartzEmitterPluginInstance
  | QuartzPageTypePluginInstance
```

接下来的几节会详细说明每种插件类型可以实现哪些方法。在此之前，先澄清几个含义不太明确的类型：

- `BuildCtx` 定义在 `@quartz-community/types` 中。它包含
  - `argv`：传递给 Quartz [[build]] 命令的命令行参数
  - `cfg`：完整的 Quartz [[configuration]]
  - `allSlugs`：所有有效内容 slug 的列表（关于 slug 是什么的更多信息，见 [[paths]]）
- `StaticResources` 定义在 `@quartz-community/types` 中。它包含
  - `css`：应加载的 CSS 样式定义列表。CSS 样式用 `CSSResource` 类型描述。它接受源 URL 或样式表的内联内容。
  - `js`：应加载的脚本列表。脚本用 `JSResource` 类型描述。它允许你定义加载时机（在 DOM 加载之前或之后）、是否为模块，以及脚本的源 URL 或内联内容。
  - `additionalHead`：要添加到页面 `<head>` 标签的 JSX 元素或返回 JSX 元素的函数组成的列表。函数接收页面数据作为参数，并可以有条件地渲染元素。

## 快速开始

在 v5 中，插件是独立的仓库。创建插件最简单的方式是使用插件模板：

```shell
# 使用插件模板在 GitHub 上创建一个新仓库
# 然后在本地克隆它
git clone https://github.com/your-username/my-plugin.git
cd my-plugin
npm install
```

模板提供了构建配置（`tsup.config.ts`）、TypeScript 设置以及正确的包结构。

## 插件结构

插件的基本文件结构如下：

```
my-plugin/
├── src/
│   └── index.ts          # 插件入口点
├── tsup.config.ts         # 构建配置
├── package.json           # 依赖与导出
└── tsconfig.json          # TypeScript 配置
```

插件的 `package.json` 应声明对 `@quartz-community/types`（用于类型定义）的依赖，并可选地声明对 `@quartz-community/utils`（用于共享工具）的依赖。

## 插件类型

## 选择插件类型

Quartz 支持六种插件能力。单个插件可以组合多种类型。

| 我想要……                                         | 插件类型    |
| ------------------------------------------------ | ----------- |
| 转换 Markdown/HTML 内容                          | Transformer |
| 决定发布哪些页面                                 | Filter      |
| 生成输出文件（RSS、站点地图、清单）              | Emitter     |
| 定义某一类页面的渲染方式                         | Page Type   |
| 向布局添加 UI 组件                               | Component   |
| 向 Bases 数据库系统添加自定义视图                | Bases View  |

这些类型**并非互斥**。例如：

- `obsidian-flavored-markdown` 既是**转换器**（处理 OFM 语法），又提供**组件**（Mermaid 渲染）
- `canvas-page` 是一个**页面类型**，同时提供自定义**框架**
- 一个插件可以既是添加元数据的**转换器**，又是展示它的**组件**

### 转换器

转换器对内容做**映射**，接收一个 Markdown 文件，输出修改后的内容，或向文件本身添加元数据。

```ts
export type QuartzTransformerPluginInstance = {
  name: string
  textTransform?: (ctx: BuildCtx, src: string) => string
  markdownPlugins?: (ctx: BuildCtx) => PluggableList
  htmlPlugins?: (ctx: BuildCtx) => PluggableList
  externalResources?: (ctx: BuildCtx) => Partial<StaticResources>
}
```

所有转换器插件至少必须定义一个 `name` 字段以注册插件，以及若干可选函数，用于挂接到转换单个 Markdown 文件的各个环节。

- `textTransform` 在文件被解析为 [Markdown AST](https://github.com/syntax-tree/mdast) _之前_ 执行文本到文本的转换。
- `markdownPlugins` 定义一组 [remark 插件](https://github.com/remarkjs/remark/blob/main/doc/plugins.md)。`remark` 是一个以结构化方式将 Markdown 转换为 Markdown 的工具。
- `htmlPlugins` 定义一组 [rehype 插件](https://github.com/rehypejs/rehype/blob/main/doc/plugins.md)。与 `remark` 的工作方式类似，`rehype` 是一个以结构化方式将 HTML 转换为 HTML 的工具。
- `externalResources` 定义插件在客户端正常工作可能需要加载的任何外部资源。

通常对于 `remark` 和 `rehype`，你都能找到现成可用的插件。如果你想自己编写 `remark` 或 `rehype` 插件，请参阅使用 `unified`（底层的 AST 解析与转换库）的[创建插件指南](https://unifiedjs.com/learn/guide/create-a-plugin/)。

一个借用 `remark` 与 `rehype` 生态的转换器插件的好例子是 [[plugins/Latex|Latex]] 插件：

```ts
import remarkMath from "remark-math"
import rehypeKatex from "rehype-katex"
import rehypeMathjax from "rehype-mathjax/svg"
import { QuartzTransformerPlugin } from "@quartz-community/types"

interface Options {
  renderEngine: "katex" | "mathjax"
}

export const Latex: QuartzTransformerPlugin<Options> = (opts?: Options) => {
  const engine = opts?.renderEngine ?? "katex"
  return {
    name: "Latex",
    markdownPlugins() {
      return [remarkMath]
    },
    htmlPlugins() {
      if (engine === "katex") {
        // 如果你需要向插件传递选项，
        // 可以使用 [plugin, options] 元组
        return [[rehypeKatex, { output: "html" }]]
      } else {
        return [rehypeMathjax]
      }
    },
    externalResources() {
      if (engine === "katex") {
        return {
          css: [
            {
              // 基础 css
              content: "https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css",
            },
          ],
          js: [
            {
              // 修复复制行为：https://github.com/KaTeX/KaTeX/blob/main/contrib/copy-tex/README.md
              src: "https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/contrib/copy-tex.min.js",
              loadTime: "afterDOMReady",
              contentType: "external",
            },
          ],
        }
      }
    },
  }
}
```

转换器插件另一件常见的事是解析文件并为该文件添加额外数据：

```ts
import { QuartzTransformerPlugin } from "@quartz-community/types"

export const AddWordCount: QuartzTransformerPlugin = () => {
  return {
    name: "AddWordCount",
    markdownPlugins() {
      return [
        () => {
          return (tree, file) => {
            // tree 是一个 `mdast` 根元素
            // file 是一个 `vfile`
            const text = file.value
            const words = text.split(" ").length
            file.data.wordcount = words
          }
        },
      ]
    },
  }
}

// 让 TypeScript 知道我们正在添加的自定义数据字段
// 其他插件随后也会知道这个数据字段
declare module "vfile" {
  interface DataMap {
    wordcount: number
  }
}
```

最后，你还可以使用 `unist-util-visit` 包中的 `visit` 函数，或 `mdast-util-find-and-replace` 包中的 `findAndReplace` 函数，对 Markdown 或 HTML AST 执行变换。

```ts
import { visit } from "unist-util-visit"
import { findAndReplace } from "mdast-util-find-and-replace"
import { QuartzTransformerPlugin } from "@quartz-community/types"
import { Link } from "mdast"

export const TextTransforms: QuartzTransformerPlugin = () => {
  return {
    name: "TextTransforms",
    markdownPlugins() {
      return [
        () => {
          return (tree, file) => {
            // 把 _text_ 替换为斜体版本
            findAndReplace(tree, /_(.+)_/, (_value: string, ...capture: string[]) => {
              // inner 是正则中 () 内的文本
              const [inner] = capture
              // 返回一个 mdast 节点
              // https://github.com/syntax-tree/mdast
              return {
                type: "emphasis",
                children: [{ type: "text", value: inner }],
              }
            })

            // 移除所有链接（仅替换为链接内容）
            // 通过 mdast 节点上的 'type' 字段匹配
            // 本例使用 https://github.com/syntax-tree/mdast#link
            visit(tree, "link", (link: Link) => {
              return {
                type: "paragraph",
                children: [{ type: "text", value: link.title }],
              }
            })
          }
        },
      ]
    },
  }
}
```

最后再说一句：转换器插件相当复杂，所以如果没能立刻理解它们，也不必担心。看看内置的转换器，观察它们如何作用于内容，从而更好地理解如何实现你想做的事情。

### 过滤器

过滤器对内容进行**过滤**，接收所有转换器的输出，并决定实际保留哪些文件、丢弃哪些文件。

```ts
export type QuartzFilterPlugin<Options extends OptionType = undefined> = (
  opts?: Options,
) => QuartzFilterPluginInstance

export type QuartzFilterPluginInstance = {
  name: string
  shouldPublish(ctx: BuildCtx, content: ProcessedContent): boolean
}
```

过滤器插件必须定义一个 `name` 字段和一个 `shouldPublish` 函数，后者接收一段已由所有转换器处理过的内容，并根据它是否应传递给输出器插件返回 `true` 或 `false`。

例如，下面是用于移除草稿的内置插件：

```ts
import { QuartzFilterPlugin } from "@quartz-community/types"

export const RemoveDrafts: QuartzFilterPlugin<{}> = () => ({
  name: "RemoveDrafts",
  shouldPublish(_ctx, [_tree, vfile]) {
    // 使用从转换器解析出的 frontmatter
    const draftFlag: boolean = vfile.data?.frontmatter?.draft ?? false
    return !draftFlag
  },
})
```

### 输出器

输出器对内容做**归约**，接收所有经过转换和过滤的内容列表，并创建输出文件。

```ts
export type QuartzEmitterPlugin<Options extends OptionType = undefined> = (
  opts?: Options,
) => QuartzEmitterPluginInstance

export type QuartzEmitterPluginInstance = {
  name: string
  emit(
    ctx: BuildCtx,
    content: ProcessedContent[],
    resources: StaticResources,
  ): Promise<FilePath[]> | AsyncGenerator<FilePath>
  partialEmit?(
    ctx: BuildCtx,
    content: ProcessedContent[],
    resources: StaticResources,
    changeEvents: ChangeEvent[],
  ): Promise<FilePath[]> | AsyncGenerator<FilePath> | null
  getQuartzComponents(ctx: BuildCtx): QuartzComponent[]
}
```

输出器插件必须定义一个 `name` 字段、一个 `emit` 函数和一个 `getQuartzComponents` 函数。它可以选择实现一个 `partialEmit` 函数用于增量构建。

- `emit` 负责查看所有已解析和过滤的内容，然后相应地创建文件，并返回该插件所创建文件的路径列表。
- `partialEmit` 是一个可选函数，用于启用增量构建。它接收关于哪些文件发生了变化的信息（`changeEvents`），并可以只重建必要的文件。这对于在开发模式下优化构建时间很有用。如果 `partialEmit` 未定义，则默认使用 `emit` 函数。
- `getQuartzComponents` 声明该输出器使用哪些 Quartz 组件来构建其页面。

创建新文件可以通过常规的 Node [fs 模块](https://nodejs.org/api/fs.html)（即 `fs.cp` 或 `fs.writeFile`）完成；如果要创建包含文本的文件，也可以通过 `@quartz-community/utils` 中的 `write` 函数完成。`write` 的签名如下：

```ts
export type WriteOptions = (data: {
  // 构建上下文
  ctx: BuildCtx
  // 要输出的文件名（不包含文件扩展名）
  slug: FullSlug
  // 文件扩展名
  ext: `.${string}` | ""
  // 要添加的文件内容
  content: string
}) => Promise<FilePath>
```

它是对「写入相应输出文件夹并确保中间目录存在」这一操作的薄封装。如果你选择使用 Node 原生的 `fs` API，请同样确保输出到 `argv.output` 文件夹。

如果你要创建的输出器插件需要渲染组件，还有三件事需要注意：

- 你的组件应使用 `getQuartzComponents` 声明它用于构建页面的 `QuartzComponents` 列表。更多信息见 [[creating components]] 页面。
- 你可以使用 `@quartz-community/utils` 中定义的 `renderPage` 函数将 Quartz 组件渲染为 HTML。
- 如果你需要将 HTML AST 渲染为 JSX，可以使用 `@quartz-community/utils` 中的 `htmlToJsx` 函数。

例如，下面是渲染每一个页面的内容页面插件的简化版本。

```tsx
import { QuartzEmitterPlugin, FullPageLayout, QuartzComponentProps } from "@quartz-community/types"
import { renderPage, canonicalizeServer, pageResources, write } from "@quartz-community/utils"

export const ContentPage: QuartzEmitterPlugin = () => {
  return {
    name: "ContentPage",
    getQuartzComponents(ctx) {
      const { head, header, beforeBody, pageBody, afterBody, left, right, footer } = ctx.cfg.layout
      return [head, ...header, ...beforeBody, pageBody, ...afterBody, ...left, ...right, footer]
    },
    async emit(ctx, content, resources): Promise<FilePath[]> {
      const cfg = ctx.cfg.configuration
      const fps: FilePath[] = []
      const allFiles = content.map((c) => c[1].data)
      for (const [tree, file] of content) {
        const slug = canonicalizeServer(file.data.slug!)
        const externalResources = pageResources(slug, file.data, resources)
        const componentData: QuartzComponentProps = {
          fileData: file.data,
          externalResources,
          cfg,
          children: [],
          tree,
          allFiles,
        }

        const content = renderPage(cfg, slug, componentData, {}, externalResources)
        const fp = await write({
          ctx,
          content,
          slug: file.data.slug!,
          ext: ".html",
        })

        fps.push(fp)
      }
      return fps
    },
  }
}
```

页面类型定义某一类页面如何渲染。它们是在 Quartz 中为新文件类型或虚拟页面添加支持的主要方式。

```ts
export interface QuartzPageTypePluginInstance {
  name: string
  priority?: number
  fileExtensions?: string[]
  match: PageMatcher
  generate?: PageGenerator
  layout: string
  frame?: string
  body: QuartzComponentConstructor
}
```

- `name`：此页面类型的唯一标识符。
- `priority`：当多个页面类型都能匹配某个 slug 时，控制匹配顺序。优先级更高的页面类型会先被检查。默认值：`0`。
- `fileExtensions`：此页面类型处理的文件扩展名数组（例如 `[".canvas"]`、`[".base"]`）。内容文件（`.md`）由默认的内容页面类型处理。
- `match`：一个函数，用于判断给定的 slug/文件是否应由该页面类型渲染。
- `generate`：一个可选函数，用于生成虚拟页面（不基于磁盘文件的页面，例如文件夹列表或标签索引）。
- `layout`：布局配置键（例如 `"content"`、`"folder"`、`"tag"`）。它决定 `quartz.config.yaml` 中哪个 `byPageType` 条目为此页面类型提供布局覆盖。
- `frame`：此页面类型要使用的 [[layout#Page Frames|页面框架]]。它控制整体 HTML 结构（例如 `"default"`、`"full-width"`、`"minimal"`，或你的插件提供的自定义框架）。如果未设置，默认为 `"default"`。可以通过 `quartz.config.yaml` 中的 `layout.byPageType.<name>.template` 按页面类型覆盖。
- `body`：渲染页面正文内容的 Quartz 组件构造函数。

### 提供自定义框架

插件可以随包提供自己的 [[layout#Page Frames|页面框架]]——即控制 HTML 结构（侧边栏、页眉、内容区、页脚）如何排列的自定义页面布局。这对于需要根本不同布局的页面类型（例如全屏画布、演示模式、仪表盘）很有用。

要提供自定义框架：

**1. 创建框架文件：**

```tsx title="src/frames/MyFrame.tsx"
import type { PageFrame, PageFrameProps } from "@quartz-community/types"
import type { ComponentChildren } from "preact"

export const MyFrame: PageFrame = {
  name: "my-frame",
  css: `
.page[data-frame="my-frame"] > #quartz-body {
  grid-template-columns: 1fr;
  grid-template-areas: "center";
}
`,
  render({ componentData, pageBody: Content, footer: Footers }: PageFrameProps): unknown {
    const renderSlot = (C: (props: typeof componentData) => unknown): ComponentChildren =>
      C(componentData) as ComponentChildren
    return (
      <div class="center">
        {(Content as any)(componentData)}
        {Footers.map((Footer) => (Footer as any)(componentData))}
      </div>
    )
  },
}
```

关键要求：

- `name`：唯一的字符串标识符。页面类型和 YAML 配置引用的就是它。
- `render()`：接收所有布局槽位（页眉、侧边栏、内容、页脚），并返回内部页面结构的 JSX。注意 `footer` 是 `QuartzComponent[]`（数组）——框架应使用 `.map()` 遍历它，以渲染所有页脚组件。
- `css`（可选）：框架专属的 CSS。使用 `.page[data-frame="my-frame"]` 选择器限定其作用范围，以避免冲突。

**2. 重新导出框架：**

```ts title="src/frames/index.ts"
export { MyFrame } from "./MyFrame"
```

**3. 在 `package.json` 中声明框架：**

```json title="package.json"
{
  "exports": {
    ".": {
      "import": "./dist/index.js",
      "types": "./dist/index.d.ts"
    },
    "./frames": {
      "import": "./dist/frames/index.js",
      "types": "./dist/frames/index.d.ts"
    }
  },
  "quartz": {
    "frames": {
      "MyFrame": { "exportName": "MyFrame" }
    }
  }
}
```

`"quartz"` 清单中的 `"frames"` 字段将导出名映射到框架元数据。键（例如 `"MyFrame"`）必须与 `src/frames/index.ts` 中的导出名匹配。

**4. 将框架入口点添加到构建配置：**

```ts title="tsup.config.ts"
export default defineConfig({
  entry: ["src/index.ts", "src/frames/index.ts"],
  // ...
})
```

**5. 在页面类型中引用框架：**

```ts
export const MyPageType: QuartzPageTypePlugin = () => ({
  name: "MyPageType",
  frame: "my-frame", // 通过框架的 name 属性引用它
  // ...
})
```

当用户安装你的插件时，Quartz 会自动从 `./frames` 导出加载框架，并将其注册到 Frame Registry 中。之后，任何页面类型或 YAML 配置覆盖都可以按名称使用该框架。

> [!tip]
> 查看 [`canvas-page`](https://github.com/quartz-community/canvas-page) 插件，了解插件提供框架的完整真实示例。

### Bases 视图

`bases-page` 插件提供了类似 Obsidian Bases 的数据库式视图系统。其他插件可以通过 `ViewRegistry` 注册自定义视图类型：

```ts
import { viewRegistry } from "@quartz-community/bases-page";
import type { ViewTypeRegistration } from "@quartz-community/bases-page";

viewRegistry.register({
  id: "timeline",
  name: "Timeline",
  icon: "git-branch",
  render: ({ entries, view, slug, allSlugs }) => (
    <div class="bases-timeline">
      {entries.map(entry => <div>{entry.properties.title}</div>)}
    </div>
  ),
  css: `.bases-timeline { display: flex; flex-direction: column; }`,
  afterDOMLoaded: `document.addEventListener("nav", () => { /* setup */ })`,
});
```

每个视图注册包含：

- `id`：唯一标识符（例如 `"timeline"`、`"kanban"`）
- `name`：在视图选择器中显示的显示名称
- `icon`：可选的 Lucide 图标名
- `render`：接收 `ViewRendererProps` 并返回 Preact JSX 的函数
- `css`：可选的 CSS 字符串（按视图 ID 去重）
- `afterDOMLoaded`：可选的客户端脚本（生命周期与组件脚本相同）
- `options`：传给每次渲染调用的可选配置

`ViewRegistry` 是一个全局单例（通过 `Symbol.for` 实现），确保模块的所有副本共享同一个注册表。

## 构建与分发

Quartz v5 插件在其仓库中附带预构建的 `dist/`。当用户安装你的插件时，Quartz 会检测到预构建输出并完全跳过安装/构建流程——使安装几乎瞬间完成。

### 构建配置

插件模板的 `tsup.config.ts` 默认打包所有依赖。只有**单例外部依赖**——必须在所有插件之间保持同一实例的包——不被打包：

```ts
const SINGLETON_EXTERNALS = [
  "preact",
  "preact/hooks",
  "preact/jsx-runtime",
  "preact/compat",
  "@jackyzha0/quartz",
  "@jackyzha0/quartz/*",
  "vfile",
  "vfile/*",
  "unified",
]

export default defineConfig({
  // ...
  noExternal: [/.*/], // 打包所有内容
  external: SINGLETON_EXTERNALS, // 单例除外
})
```

这意味着你插件的 `dist/index.js` 是自包含的——安装时无需运行 `npm install`。

### 交付预构建输出

你插件的 `dist/` 目录应提交到仓库：

1. **不要将 `dist/` 添加到 `.gitignore`**
2. 在提交前运行 `npm run build`
3. CI 工作流会在每次推送时验证 `dist/` 是最新的

如果 `dist/` 缺失或被 gitignore 忽略，Quartz 会回退到完整的安装/构建流程（在使用符号链接插件进行本地开发时很有用）。

### 带原生依赖的插件

需要原生包的插件（例如用于图像处理的 `sharp`）无法打包这些包。对于这类插件：

1. 在 `package.json` 的 quartz 清单中设置 `"requiresInstall": true`
2. 将该原生包声明为 `peerDependency`
3. Quartz 会在构建时将其安装到宿主项目中

```shell
# 构建插件
npm run build
# 或者
npx tsup
```

## 从哪里导入什么

| 你需要……                                                               | 从何处导入                                                        |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------- |
| 类型定义（`QuartzTransformerPlugin`、`QuartzComponent` 等）            | `@quartz-community/types`                                         |
| 路径工具（`simplifySlug`、`resolveRelative`、`pathToRoot`）            | `@quartz-community/utils/path`                                    |
| DOM 工具（`removeAllChildren`、`registerEscapeHandler`）               | `@quartz-community/utils/dom`                                     |
| JSX 转换（`htmlToJsx`）                                                | `@quartz-community/utils/jsx`                                     |
| 语言工具（`classNames`、`capitalize`）                                 | `@quartz-community/utils/lang`                                    |
| 日期/排序工具（`formatDate`、`getDate`、`byDateAndAlphabetical`）      | `@quartz-community/utils/date` 和 `@quartz-community/utils/sort`  |
| HTML 转义（`escapeHTML`、`unescapeHTML`）                              | `@quartz-community/utils/escape`                                  |
| Emoji 工具（`getIconCode`）                                            | `@quartz-community/utils/emoji`                                   |
| 浏览器运行时（`onNav`、`onRender`、`fetchContentIndex`）               | `@quartz-community/runtime`                                       |

**不要**直接从 `@jackyzha0/quartz` 或 `vfile` 导入。请改用社区包。

## 国际化（i18n）

插件应为自己面向用户的字符串提供翻译。**不要**在组件中硬编码字符串。

### 设置 i18n

创建以下结构：

```
src/i18n/
├── index.ts
└── locales/
    └── en-US.ts
```

**`src/i18n/locales/en-US.ts`**（必需的基础语言区域）：

```ts
export default {
  components: {
    myPlugin: {
      title: "My Plugin",
      description: "A description",
      itemCount: ({ count }: { count: number }) => (count === 1 ? "1 item" : `${count} items`),
    },
  },
}
```

**`src/i18n/index.ts`**：

```ts
import enUS from "./locales/en-US"

const locales: Record<string, typeof enUS> = {
  "en-US": enUS,
}

export function i18n(locale: string) {
  return locales[locale] || enUS
}
```

### 在组件中使用 i18n

```tsx
import { i18n } from "../i18n"

const MyComponent: QuartzComponent = ({ cfg }) => {
  const locale = cfg.locale ?? "en-US"
  const t = i18n(locale).components.myPlugin
  return <h2>{t.title}</h2>
}
```

### 添加翻译

要添加新的语言区域，复制 `en-US.ts`，翻译其中的字符串，然后注册它：

```ts
// src/i18n/locales/fr-FR.ts
export default {
  components: {
    myPlugin: {
      title: "Mon Plugin",
      description: "Une description",
      itemCount: ({ count }: { count: number }) =>
        count === 1 ? "1 élément" : `${count} éléments`,
    },
  },
}
```

```ts
// src/i18n/index.ts
import enUS from "./locales/en-US"
import frFR from "./locales/fr-FR"

const locales: Record<string, typeof enUS> = {
  "en-US": enUS,
  "fr-FR": frFR,
}
```

使用 [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) 语言区域代码（例如 `en-US`、`de-DE`、`ja-JP`、`zh-CN`）。对于动态内容，使用如上文 `itemCount` 所示的基于函数的翻译。

## 安装你的插件

```shell
# 在你的 Quartz 项目中
npx quartz plugin add github:your-username/my-plugin
```

这会克隆插件，并将其添加到 `quartz.config.yaml` 和 `quartz.lock.json` 中。如果插件附带预构建的 `dist/`（推荐），安装会在数秒内完成，无需构建步骤。随后你可以在配置中设置它：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:your-username/my-plugin
    enabled: true
```

对于需要 JavaScript 回调函数（无法在 YAML 中表达）的选项，请使用 `quartz.ts` 中的 TS 覆盖：

```ts title="quartz.ts (override)"
import * as ExternalPlugin from "./.quartz/plugins"

// 必须放在 loadQuartzConfig() 之前
ExternalPlugin.MyPlugin({
  customFn: (data) => {
    // ...
  },
})
```

通过 `quartz.ts` 设置的选项会在实例化时与 YAML 选项合并，其中 `quartz.ts` 的覆盖优先。这些调用必须放在 `quartz.ts` 中 `loadQuartzConfig()` **之前**。

### 开发工作流

在插件开发过程中，你会频繁安装和卸载插件以测试改动。以下命令有助于管理这个循环：

```shell
# 移除插件并清理
npx quartz plugin remove my-plugin

# 修改后重新添加
npx quartz plugin add github:your-username/my-plugin
```

如果你更新了 `quartz.config.yaml` 以引用一个尚未安装的插件，可以无需手动运行 `add` 就安装它：

```shell
# 安装配置中引用但 lockfile 中缺失的所有插件
npx quartz plugin install --from-config

# 先预览，不做任何改动
npx quartz plugin install --from-config --dry-run
```

要清理已安装但配置中不再引用的插件：

```shell
# 移除孤立插件
npx quartz plugin prune

# 先预览，不做任何改动
npx quartz plugin prune --dry-run
```

> [!tip]
> 如果不存在 `quartz.config.yaml`，`resolve` 和 `prune` 都会回退到 `quartz.config.default.yaml`。这在以默认配置为唯一依据的 CI 环境中很有用。完整细节见 [[cli/plugin#prune|prune]] 和 [[cli/plugin#resolve|resolve]]。

## 组件插件

对于提供可视组件（如 Explorer、Graph、Search）的插件，请参阅 [[creating components|创建组件插件]] 指南。

纯组件插件（清单中包含 `"category": ["component"]` 的插件）通过副作用导入加载，而不是通过工厂函数。如果你的纯组件插件需要从 `quartz.config.yaml` 接收用户选项，请导出一个 `init(options)` 函数——详见 [[creating components#Receiving YAML Options in Component-Only Plugins|接收 YAML 选项]]。
