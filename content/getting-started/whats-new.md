---
title: "Quartz 5 新特性"
aliases:
  - "changelog"
  - "v5"
---

Quartz 5 是对 Quartz 的彻底重构，重点关注可扩展性、性能和 Obsidian 兼容性。如果你是从 v4 过来，请参见 [[migrating|迁移到 Quartz 5]] 了解升级路径。

## 插件生态

v5 中最大的变化是转向 **社区插件生态**。插件现在是独立的包，由 [quartz-community](https://github.com/quartz-community) GitHub 组织维护，并通过 git 安装：

```bash
npx quartz plugin add github:quartz-community/explorer
```

这意味着：

- **独立版本管理**：插件可以在不升级 Quartz 本身的情况下更新
- **社区贡献**：任何人都可以发布 Quartz 插件
- **更小的核心**：Quartz 核心更精简，功能都放在插件里
- **插件注册表**：通过 `npx quartz tui` 或[插件注册表](https://github.com/quartz-community/registry)发现插件

Quartz 附带 40 多个官方插件，涵盖从搜索和关系图谱到加密页面和 Canvas 渲染的各种功能。

## YAML 配置

配置从 TypeScript（`quartz.config.ts`）迁移到 **YAML**（`quartz.config.yaml`）：

```yaml title="quartz.config.yaml"
configuration:
  pageTitle: My Digital Garden
  enableSPA: true
  enablePopovers: true
  locale: en-US
  baseUrl: mysite.github.io
  theme:
    typography:
      header: Schibsted Grotesk
      body: Source Sans Pro
      code: IBM Plex Mono
plugins:
  - source: github:quartz-community/obsidian-flavored-markdown
    enabled: true
    order: 30
  - source: github:quartz-community/explorer
    enabled: true
    layout:
      position: left
      priority: 50
```

优势：

- **基础自定义无需 TypeScript 知识**
- **JSON Schema 校验** —— 支持 YAML 的编辑器会内联显示错误
- **按插件定义布局** —— 每个插件声明自己的位置和优先级
- **模板** —— `npx quartz create` 提供预配置的模板（default、obsidian、ttrpg、blog）

对于需要 JavaScript 的高级选项（回调、自定义组件），`quartz.ts` 覆盖系统提供了完整的编程控制。

## 改进的 Obsidian 兼容性

Quartz 5 力求完全兼容 Obsidian 的核心功能：

- **维基链接** —— 所有变体，包括别名、标题、块引用以及表格中的管道符转义
- **标注块（callout）** —— 所有内置类型、可折叠变体以及嵌套标注块
- **高亮** —— `==highlighted text==` 语法
- **注释** —— `%%hidden comments%%`（行内和块级）
- **标签** —— `#tag` 和 `#nested/tag`，并支持标签页面
- **自定义任务字符** —— `[?]`、`[!]`、`[>]` 等会保留为 `data-task` 属性
- **Mermaid 图表** —— 渲染时带有展开按钮
- **YouTube 和推文嵌入** —— 通过图片语法实现
- **块引用** —— `^block-id`，支持广泛的字符
- **视频/音频嵌入** —— 完整格式支持（mp4、webm、ogv、mov、mkv、avi、flac、aac 等）
- **Canvas 文件** —— 通过 canvas-page 插件渲染为可交互、可平移的页面
- **Obsidian URI 链接** —— 标记了 CSS 类，便于自定义样式
- **脚注** —— 通过 GitHub Flavored Markdown 插件实现

完整列表见 [[Obsidian compatibility]]。

## 页面类型系统

Quartz 5 引入了 **页面类型** —— 定义不同种类页面如何渲染的插件：

- **内容页** —— 常规 Markdown 笔记
- **文件夹页** —— 目录列表页面
- **标签页** —— 列出具有给定标签的笔记的页面
- **Canvas 页面** —— 可交互的 JSON Canvas 渲染
- **Bases 页面** —— 你内容的数据库式视图

每种页面类型都可以使用不同的 [[layout#Page Frames|页面框架]]，以呈现根本不同的 HTML 结构（三栏、全宽、极简等）。

## 布局系统

布局系统现在是声明式的。插件在配置中声明自己的位置（`left`、`right`、`beforeBody`、`afterBody`）和优先级：

```yaml
plugins:
  - source: github:quartz-community/explorer
    layout:
      position: left
      priority: 50
  - source: github:quartz-community/graph
    layout:
      position: right
      priority: 10
```

其他功能：

- **分组** —— 把组件组合成 flex 行或列（例如包含搜索和深色模式切换的工具栏）
- **条件渲染** —— 根据页面属性显示或隐藏组件（`condition: not-index`、`condition: has-tags`）
- **显示修饰符** —— `display: mobile-only` 或 `display: desktop-only`
- **按页面类型覆盖** —— 为内容页、文件夹页、标签页和 404 页面使用不同的布局

## 性能

- **并行处理** —— Markdown 解析使用覆盖所有 CPU 核心的工作线程池
- **增量重建** —— 监视模式只重新处理发生更改的文件
- **预构建插件** —— 社区插件附带已编译的 `dist/` 目录，安装时跳过从源码构建
- **SPA 路由** —— 使用 `micromorph` 实现客户端导航，页面切换即时完成
- **CDN 缓存的字体** —— Google Fonts 并启用激进缓存，或通过 `fontOrigin: local` 完全自托管

## CLI 改进

CLI 更简单、更有帮助：

| 命令 | 描述 |
| --- | --- |
| `npx quartz create` | 带模板的交互式设置向导 |
| `npx quartz build --serve` | 构建并启动服务，支持热重载 |
| `npx quartz sync` | 提交并推送到 GitHub |
| `npx quartz upgrade` | 拉取最新的 Quartz 更新 |
| `npx quartz plugin install` | 从锁文件安装插件 |
| `npx quartz plugin add <source>` | 添加新插件 |
| `npx quartz plugin list` | 列出已安装的插件 |
| `npx quartz plugin prune` | 移除未使用的插件 |

其他改进：

- **Node.js 版本检查** —— 在 Node < 22 上运行时给出清晰的错误信息
- **端口冲突处理** —— 端口已被占用时给出有用的提示信息
- **插件锁文件** —— `quartz.lock.json` 锁定插件版本，以实现可复现的构建
- **并发控制** —— `--concurrency` 标志，适用于内存受限的环境

## 国际化

Quartz 5 开箱即支持多种语言环境。在配置中设置 `locale: ja-JP`（或任何受支持的语言环境），即可翻译所有 UI 字符串 —— 搜索占位符、「目录」、日期格式等。

## 新插件

v5 新增的插件（v4 中没有）：

```base
filters:
  and:
    - file.ext == "md"
    - file.inFolder("plugins")
    - note["new-in-v5"] == true
properties:
  title:
    displayName: Plugin
  repository:
    displayName: Repository
  description:
    displayName: Description
views:
  - type: table
    name: New in v5
    order:
      - title
      - repository
      - description
    sort:
      - property: title
        direction: ASC
```

## 面向插件开发者

如果你为 v4 开发过插件，开发模式已经发生了显著变化：

- 插件是 **独立的 npm 包**，拥有自己的 `package.json`、`tsconfig.json` 和构建系统
- **工厂函数模式**（受 Astro 集成启发）取代了基于类的插件
- **`@quartz-community/types`** 提供完整的类型安全，且不依赖 Quartz 核心
- **`@quartz-community/utils`** 提供共享的路径、DOM 和语言工具
- **`@quartz-community/runtime`** 提供浏览器运行时工具
- 插件可以附带 **组件**、**框架**、**样式表** 和 **客户端脚本**
- **插件模板** 位于 [quartz-community/plugin-template](https://github.com/quartz-community/plugin-template)

完整指南见 [[making plugins]]。
