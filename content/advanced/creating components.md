---
date: 2026-09-20
title: 创建组件插件
---

> [!warning]
> 本指南假定你具备编写 JavaScript 的经验，并熟悉 TypeScript。

通常，在 Web 上我们使用 HTML 编写布局代码，看起来类似下面这样：

```html
<article>
  <h1>An article header</h1>
  <p>Some content</p>
</article>
```

这段 HTML 表示一篇文章，开头是一个内容为「An article header」的标题，以及一个包含文本「Some content」的段落。它再与 CSS 结合来为页面设置样式，与 JavaScript 结合来添加交互性。

然而，HTML 无法让你创建可复用的模板。如果你想创建一个新页面，就需要复制粘贴上面的代码片段，并自行修改标题和内容。如果站点上有大量共享相似布局的内容，这种做法并不理想。创建 React 的那些聪明人也有类似的抱怨，于是发明了组件（Components）的概念——返回 JSX 的 JavaScript 函数——来解决代码重复的问题。

实际上，组件让你可以编写一个 JavaScript 函数，接收一些数据并输出 HTML。**虽然 Quartz 并不使用 React，但它采用同样的组件概念，让你可以轻松地在 Quartz 站点中表达布局模板。**

## 社区组件插件

在 v5 中，大多数组件都是社区插件——即导出 `QuartzComponent` 的独立仓库。这些插件与 Quartz 核心仓库解耦，便于维护和共享。

### 快速开始

要创建新的组件插件，可以使用官方插件模板：

```shell
git clone https://github.com/quartz-community/plugin-template.git my-component
cd my-component
npm install
```

### 插件结构

组件插件的 `src/index.ts` 通常导出一个返回 `QuartzComponent` 的函数（构造函数）。这样用户就可以向你的组件传递配置选项。

```tsx title="src/index.ts"
import {
  QuartzComponent,
  QuartzComponentConstructor,
  QuartzComponentProps,
} from "@quartz-community/types"

interface Options {
  favouriteNumber: number
}

const defaultOptions: Options = {
  favouriteNumber: 42,
}

const MyComponent: QuartzComponentConstructor<Options> = (userOpts?: Options) => {
  const opts = { ...defaultOptions, ...userOpts }

  const Component: QuartzComponent = (props: QuartzComponentProps) => {
    if (opts.favouriteNumber < 0) return null
    return <p>My favourite number is {opts.favouriteNumber}</p>
  }

  return Component
}

export default MyComponent
```

### Props

所有 Quartz 组件都接受同一组 props：

```tsx
export type QuartzComponentProps = {
  fileData: QuartzPluginData
  cfg: GlobalConfiguration
  tree: Node<QuartzPluginData>
  allFiles: QuartzPluginData[]
  displayClass?: "mobile-only" | "desktop-only"
}
```

- `fileData`：插件可能添加到当前页面的任何元数据。
  - `fileData.slug`：当前页面的 slug。
  - `fileData.frontmatter`：解析出的任何 frontmatter。
- `cfg`：`quartz.config.yaml` 中的 `configuration` 字段。
- `tree`：文件经过处理和转换后得到的 [HTML AST](https://github.com/syntax-tree/hast)。
- `allFiles`：所有已解析文件的元数据。可用于生成页面列表或了解站点的整体结构。
- `displayClass`：一个工具类，表示用户对在移动端或桌面端环境下如何渲染该组件的偏好。

### 样式

在社区插件中，样式与插件打包在一起。你可以使用组件上的 `.css` 属性定义样式：

```tsx
Component.css = `
  .my-component { color: red; }
`
```

对于 SCSS，你可以导入它并将其赋值给 `.css` 属性。构建系统会处理转换：

```tsx
import styles from "./styles.scss"
Component.css = styles
```

> [!warning]
> Quartz 不使用 CSS 模块，因此你在这里声明的任何样式都会 _全局_ 生效。如果你只想让它作用于你的组件，请确保使用特定的类名和选择器。

### 国际化

组件插件对所有面向用户的字符串都应使用 i18n 模式。完整的设置指南见 [[making plugins#国际化（i18n）]]。

快速参考：

```tsx
import { i18n } from "../i18n"

const MyComponent: QuartzComponent = ({ cfg }) => {
  const t = i18n(cfg.locale ?? "en-US").components.myComponent
  return <h2>{t.title}</h2>
}
```

始终至少提供一个 `en-US` 语言区域作为回退。其他语言区域是可选的，但为扩大国际化覆盖范围，建议提供。

### 脚本与交互

为了实现交互，你可以在组件上声明 `.beforeDOMLoaded` 和 `.afterDOMLoaded` 属性。它们应当是包含在浏览器中执行的 JavaScript 的字符串。

- `.beforeDOMLoaded`：在页面加载完成 _之前_ 执行。用于预取或提前初始化。
- `.afterDOMLoaded`：在页面完全加载后执行一次。

如果你需要创建一个 `afterDOMLoaded` 脚本，而它依赖于在导航时可能发生变化的页面特定元素，请监听 `"nav"` 事件：

```ts
document.addEventListener("nav", () => {
  // 在这里编写页面特定的逻辑
  const toggleSwitch = document.querySelector("#switch") as HTMLInputElement
  if (toggleSwitch) {
    toggleSwitch.addEventListener("change", switchTheme)
    window.addCleanup(() => toggleSwitch.removeEventListener("change", switchTheme))
  }
})
```

你也可以使用 `"prenav"` 事件，它在 SPA 导航过程中页面被替换之前触发。

`"render"` 事件在 DOM 于未发生完整导航的情况下就地更新时触发——例如内容解密之后，或其他插件动态修改 DOM 之后。如果你的组件向内容元素附加了事件监听器，请除 `"nav"` 之外也监听 `"render"`，以确保重新初始化：

```ts
function setupMyComponent() {
  const elements = document.querySelectorAll(".my-interactive")
  for (const el of elements) {
    el.addEventListener("click", handleClick)
    window.addCleanup(() => el.removeEventListener("click", handleClick))
  }
}

document.addEventListener("nav", setupMyComponent)
document.addEventListener("render", setupMyComponent)
```

最佳实践是通过 `window.addCleanup` 跟踪所有事件处理器，以防止 SPA 导航期间发生内存泄漏。

#### 导入代码

在社区插件中，TypeScript 脚本应在构建时转译。插件模板在 `tsup.config.ts` 中包含一个 `inlineScriptPlugin`，它会自动转译以文本方式导入的 `.inline.ts` 文件：

```tsx title="src/index.ts"
import script from "./script.inline.ts"

const Component: QuartzComponent = (props) => {
  return <button id="btn">Click me</button>
}
Component.afterDOMLoaded = script
```

`inlineScriptPlugin` 在构建步骤中将 TypeScript 转译为浏览器兼容的 JavaScript，让你可以编写类型安全的客户端代码。

### 安装你的组件

组件发布后（例如发布到 GitHub 或 npm），用户可以使用 Quartz CLI 安装它：

```shell
npx quartz plugin add github:your-username/my-component
```

然后，他们可以将其添加到 `quartz.config.yaml`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:your-username/my-component
    enabled: true
    options:
      favouriteNumber: 42
    layout:
      position: left
      priority: 60
```

若要使用 `quartz.ts` 中的 TS 覆盖实现高级用法：

```ts title="quartz.ts (override)"
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"
import Plugin from "./.quartz/plugins"

const config = await loadQuartzConfig()
export default config
export const layout = await loadQuartzLayout({
  byPageType: {
    content: {
      left: [Plugin.MyComponent({ favouriteNumber: 42 })],
    },
  },
})
```

### 在纯组件插件中接收 YAML 选项

同时属于某个处理类别（转换器、过滤器、输出器、页面类型）的组件插件会自动通过其工厂函数接收选项。然而，**纯组件插件**——即清单中只声明 `"category": ["component"]` 的插件——是通过副作用导入加载的，不会经过工厂函数路径。

要在纯组件插件中接收 YAML 选项，请从入口点导出一个 `init` 函数：

```ts title="src/index.ts"
export function init(options?: Record<string, unknown>): void {
  // options 包含合并后的 defaultOptions 与用户的 YAML 选项
  const myFlag = (options?.myFlag as boolean) ?? false
  // 使用 options 配置注册、全局状态等
}
```

Quartz 的 config-loader 在导入模块后调用 `init()`，传入清单中 `defaultOptions` 与用户在 `quartz.config.yaml` 中提供的 `options` 的合并结果。合并遵循与处理类插件相同的 `{ ...defaultOptions, ...userOptions }` 模式——用户的值优先。

在 `package.json` 中声明默认值：

```json title="package.json"
{
  "quartz": {
    "category": ["component"],
    "defaultOptions": {
      "myFlag": false
    }
  }
}
```

如果你的插件不导出 `init`，它仍会作为纯粹的副作用导入正常工作——这完全向后兼容。

## 内置组件

Quartz 还有一些提供布局工具的组件。它们位于 `quartz/components/`，主要用于结构性目的：

- `Component.Head()`——渲染 `<head>` 标签
- `Component.Spacer()`——添加弹性空间
- `Component.Flex()`——弹性布局容器
- `Component.MobileOnly()`——仅在移动端显示组件
- `Component.DesktopOnly()`——仅在桌面端显示组件
- `Component.ConditionalRender()`——根据页面数据条件渲染

关于这些工具的更多细节，见 [[layout-components]]。

> [!hint]
> 可以参考现有的社区插件，比如 [Explorer](https://github.com/quartz-community/explorer) 或 [Darkmode](https://github.com/quartz-community/darkmode)，以了解真实的示例。
