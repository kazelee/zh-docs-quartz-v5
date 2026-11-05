---
title: 架构
---

Quartz 是一个静态站点生成器。它是如何工作的？

这个问题最好的回答方式是跟踪用户（也就是你！）在命令行中运行 `npx quartz build` 时发生了什么：

## 服务端

1. 运行 `npx quartz build` 后，npm 会查看 `package.json`，找到 `quartz` 的 `bin` 条目，它指向 `./quartz/bootstrap-cli.mjs`。
2. 该文件顶部有一行 [shebang](<https://en.wikipedia.org/wiki/Shebang_(Unix)>)，告诉 npm 使用 Node 执行它。
3. `bootstrap-cli.mjs` 负责几件事：
   1. 使用 [yargs](http://yargs.js.org/) 解析命令行参数。`plugin` 子命令也在这里处理，用于管理外部插件。
   2. 使用 [esbuild](https://esbuild.github.io/) 将 Quartz 的其余部分（用 TypeScript 编写）转译并打包为普通 JavaScript。这里的 `esbuild` 配置略有些特殊，因为它还要通过 [esbuild-sass-plugin v2](https://www.npmjs.com/package/esbuild-sass-plugin) 处理 `.scss` 文件的导入。此外，组件声明的「行内」客户端脚本（任何 `.inline.ts` 文件）由一个自定义 `esbuild` 插件打包，该插件会运行另一个 `esbuild` 实例，为浏览器而不是 `node` 打包。这两类模块都以纯文本形式导入。
   3. 如果设置了 `--serve`，则运行本地预览服务器。它会启动两个服务器：
      1. 端口 3001 上的 WebSocket 服务器，用于处理热重载信号。它跟踪所有入站连接，并在检测到服务端变更（内容或配置）时发送一条「rebuild」消息。
      2. 用户指定端口（通常是 8080）上的 HTTP 文件服务器，用于提供实际的网站文件。
   4. 如果设置了 `--serve` 标志，它还会启动一个文件监听器来检测源代码变更（例如任何 `.ts`、`.tsx`、`.scss` 或包管理器文件）。发生变更时，我们使用 esbuild 的 [rebuild API](https://esbuild.github.io/api/#rebuild) 重新构建模块（上面的第 2 步），这能大幅缩短构建时间。
   5. 转译主 Quartz 构建模块（`quartz/build.ts`）后，我们将其写入缓存文件 `.quartz-cache/transpiled-build.mjs`，然后用 `await import(cacheFile)` 动态导入它。不过，我们需要相当巧妙地绕过 Node 的[导入缓存](https://github.com/nodejs/modules/issues/307)，因此添加一个随机查询字符串，让 Node 误以为这是一个新模块。但这确实会造成内存泄漏，所以我们只能希望用户不会在单次会话中太多次热重载配置 :))（每次重载大约泄漏 350kB 内存）。导入该模块后，我们调用它，并传入之前解析的命令行参数，以及一个用于通知客户端刷新的回调函数。
4. 在 `build.ts` 中，我们先手动安装 source map 支持，以应对前面引入的查询字符串缓存破坏 hack。然后，开始处理内容：
   1. 清理输出目录。
   2. 递归 glob `content` 文件夹中的所有文件，并遵循 `.gitignore`。
   3. 解析 Markdown 文件。
      1. Quartz 会检测可用的线程数，如果待解析的内容超过 128 份（粗略的经验判断），就选择创建 worker 线程。如果需要创建 worker，它会再次调用 esbuild 转译 worker 脚本 `quartz/worker.ts`。然后创建一个工作窃取式的 [workerpool](https://www.npmjs.com/package/workerpool)，并以 128 个文件为一批分配给 worker。
      2. 每个 worker（如果没有并发，则只有主线程）会根据 [[configuration]] 中定义的插件创建一个 [unified](https://github.com/unifiedjs/unified) 解析器。
      3. 解析有三个步骤：
         1. 将文件读入 [vfile](https://github.com/vfile/vfile)。
         2. 对内容应用插件定义的文本转换。
         3. 对文件路径做 slugify 并存入该文件的数据中。关于 Quartz 中路径逻辑如何工作，详见 [[paths]] 页面（剧透：很复杂）。
         4. 使用 [remark-parse](https://www.npmjs.com/package/remark-parse) 解析 Markdown（文本转 [mdast](https://github.com/syntax-tree/mdast)）。
         5. 应用插件定义的 Markdown 到 Markdown 的转换。
         6. 使用 [remark-rehype](https://github.com/remarkjs/remark-rehype) 将 Markdown 转换为 HTML（[mdast](https://github.com/syntax-tree/mdast) 转 [hast](https://github.com/syntax-tree/hast)）。
         7. 应用插件定义的 HTML 到 HTML 的转换。
   4. 使用插件过滤掉不需要的内容。
   5. 使用插件输出文件。
      1. 收集每个输出器插件声明的所有静态资源（例如外部 CSS、JS 模块等）。
      2. 输出 HTML 文件的输出器在这里会做一些额外工作，因为它们需要把解析步骤产生的 [hast](https://github.com/syntax-tree/hast) 转换成 JSX。这通过 [hast-util-to-jsx-runtime](https://github.com/syntax-tree/hast-util-to-jsx-runtime) 和 [Preact](https://preactjs.com/) 运行时完成。最后，使用 [preact-render-to-string](https://github.com/preactjs/preact-render-to-string) 将 JSX 渲染为 HTML，它会静态地把 JSX 渲染成 HTML（也就是说，不关心 `useState`、`useEffect` 或任何其他 React/Preact 交互部分）。在这里，我们还做了许多有趣的事，比如从 `quartz.config.yaml` 组装页面 [[layout]]、组装所有实际发送到客户端的行内脚本，以及所有转译后的样式。这大部分逻辑位于 `quartz/components/renderPage.tsx`。其他值得注意的有趣之处：
         1. CSS 使用 [Lightning CSS](https://github.com/parcel-bundler/lightningcss) 进行压缩和转换，以添加厂商前缀并做语法降级。
         2. 脚本被拆分为 `beforeDOMLoaded` 和 `afterDOMLoaded`，分别插入到 `<head>` 和 `<body>` 中。
      3. 最后，每个输出器插件负责把自己输出的文件生成并写入磁盘。
   6. 如果检测到 `--serve` 标志，我们还会设置另一个文件监听器来检测内容变更（仅 `.md` 文件）。我们维护一个内容映射，记录每个 slug 解析后的 AST 和插件数据，并在文件变更时更新它。新增或修改的路径会被重新构建并加入内容映射。然后，对得到的内容映射运行所有过滤器和输出器。该文件监听器带有 250ms 阈值的防抖。成功后，我们使用传入的回调函数向客户端发送刷新信号。

## 客户端

1. 浏览器打开一个 Quartz 页面并加载 HTML。`<head>` 还会链接页面样式（输出到 `public/index.css`）和页面关键 JS（输出到 `public/prescript.js`）
2. 然后，body 加载完成后，浏览器加载非关键 JS（输出到 `public/postscript.js`）
3. 页面加载完成后，会派发一个自定义的合成浏览器事件 `"nav"`。这样，组件声明的客户端脚本就可以「设置」任何需要访问页面 DOM 的内容。
   1. 如果在 [[configuration]] 中启用了 [[SPA Routing|enableSPA 选项]]，那么在任何客户端导航时也会触发该 `"nav"` 事件，以便组件注销并重新注册事件处理器和状态。
   2. 如果没有启用，我们把 `"nav"` 事件接成只在页面加载后触发一次，以保持 SPA 与非 SPA 上下文中状态设置方式的一致性。
   3. 当 DOM 在原地更新而没有完整导航时（例如内容解密后），可以派发一个单独的 `"render"` 事件。为内容元素绑定监听器的组件应同时监听 `"nav"` 和 `"render"`。

## 社区包的分层

Quartz v5 把共享代码拆分为三个社区包，每个包有明确的职责：

- **`@quartz-community/types`** — 类型定义、接口，以及规范的 `vfile` DataMap 扩展。它是 Quartz 与插件之间的「契约」。没有运行时依赖。
- **`@quartz-community/utils`** — 共享工具函数（路径操作、DOM 辅助函数、排序、日期格式化、JSX 转换等）。依赖 `@quartz-community/types`。
- **`@quartz-community/runtime`** — 供客户端脚本使用的仅浏览器端工具（事件处理、导航、存储、脚本加载）。同时依赖 `types` 与 `utils`。

```
types (no deps)
  ↑
utils (depends on types)
  ↑
runtime (depends on types + utils)
  ↑
plugins (depend on any combination)
```

插件应从 `@quartz-community/types` 导入类型，从 `@quartz-community/utils` 导入工具函数，从 `@quartz-community/runtime` 导入浏览器端工具。这种分层确保插件不依赖 Quartz 核心。

## 插件系统

页面类型定义某一类页面如何渲染。它们在 `quartz.config.yaml` 的 `pageTypes` 数组中配置。

Quartz v5 引入了社区插件系统。插件是独立的 Git 仓库，会被克隆到 `.quartz/plugins/`，并通过自动生成的索引文件 `.quartz/plugins/index.ts` 重新导出。

### 插件类型

现在有四类插件：

- **转换器**：对内容做映射（解析 frontmatter、生成描述、语法高亮）
- **过滤器**：过滤内容（移除草稿、显式发布）
- **输出器**：对内容做归约（生成 RSS、站点地图、别名重定向、OG 图片）
- **页面类型**：定义页面如何渲染。每种页面类型处理特定类型的页面（内容笔记、文件夹列表、标签列表、404）。`PageTypeDispatcher` 输出器会根据内容把页面路由到合适的页面类型插件。
- **Bases 视图**：为 `bases-page` 插件的类数据库视图系统提供的自定义视图渲染器。插件可以通过 `ViewRegistry` 注册新的视图类型（例如时间线、看板）。详见 [[making plugins#Bases Views]]。

注意，插件类型**并非互斥**——同一个插件既可以是转换器并提供组件（例如 `obsidian-flavored-markdown`），也可以是页面类型并提供自定义框架（例如 `canvas-page`）。

### 插件解析

当运行 `npx quartz plugin add github:quartz-community/explorer` 时：

1. 仓库被克隆到 `.quartz/plugins/explorer/`
2. 插件使用 `tsup` 构建（定义在每个插件的 `tsup.config.ts` 中）
3. 自动生成的 `.quartz/plugins/index.ts` 重新导出所有已安装的插件
4. 插件的提交哈希记录在 `quartz.lock.json` 中

### 插件 CLI 命令

- `npx quartz plugin add github:quartz-community/<name>` — 安装一个社区插件
- `npx quartz plugin install --latest` — 将所有插件更新到最新提交
- `npx quartz plugin install --clean` — 从 `quartz.lock.json` 中锁定的提交恢复插件（用于 CI/CD）
- `npx quartz plugin remove <name>` — 移除已安装的插件

### 插件结构

每个社区插件仓库包含：

- `src/index.ts` — 插件入口，导出插件函数
- `tsup.config.ts` — 使用 tsup 的构建配置
- `package.json` — 声明对 `@quartz-community/types` 与 `@quartz-community/utils` 的依赖

插件系统的架构与设计在此有意保持较为简略，因为 [[making plugins|创建插件]] 指南中有更深入的描述。

## 页面框架

页面框架控制每个页面的内部 HTML 结构。外层外壳（`<html>`、`<head>`、`<body>`、`#quartz-root`）始终相同（[[SPA Routing]] 需要如此），而框架决定页面内部各布局槽位如何排列。

框架系统位于 `quartz/components/frames/`，包含：

- `types.ts` — 定义 `PageFrame` 与 `PageFrameProps` 接口
- `DefaultFrame.tsx` — 三栏布局（左侧栏、中间、右侧栏、页脚）
- `FullWidthFrame.tsx` — 无侧边栏，仅单列居中
- `MinimalFrame.tsx` — 无侧边栏，无 header/beforeBody，只有内容和页脚
- `registry.ts` — 用于插件注册框架的 `FrameRegistry` 单例
- `index.ts` — `resolveFrame()` 函数与内置框架注册表

### 框架注册表

`FrameRegistry`（`quartz/components/frames/registry.ts`）是一个单例，存储社区插件注册的框架。它的设计与 `ComponentRegistry` 一致。插件在 `package.json` 清单的 `"quartz"."frames"` 字段中声明框架，这些框架在插件初始化时由 `quartz/plugins/loader/frameLoader.ts` 加载。

### 框架解析

`quartz/components/renderPage.tsx` 中的渲染流水线会委托给已解析框架的 `render()` 函数。框架解析在 `PageTypeDispatcher` 输出器（`quartz/plugins/pageTypes/dispatcher.ts`）中完成，优先级如下：

1. YAML 配置：`layout.byPageType.<name>.template`
2. 插件注册的框架：在 `FrameRegistry` 中按名称查找
3. 内置框架：在 `builtinFrames` 映射中按名称查找
4. 回退：`"default"`

当前生效的框架名称会作为 `data-frame` 属性设置在 `.page` 元素上，从而支持在 `quartz/styles/base.scss` 中做针对框架的 CSS 覆盖。

### 插件提供的框架

社区插件可以自带框架：从 `./frames` 子路径导出，并在插件清单中声明。例如，`canvas-page` 插件提供了 `"canvas"` 框架，具有全屏布局和可切换的侧边栏。实现细节见 [[making plugins#Providing Custom Frames]]。

面向用户的文档见 [[layout#Page Frames]]，关于如何在页面类型插件中设置框架见 [[making plugins#Page Types]]。
