---
title: "迁移到 Quartz 5"
aliases:
  - "migrating from Quartz 3"
  - "migrating from Quartz 4"
---

本指南介绍如何从更早的版本迁移到 Quartz 5。如果你已经在使用 Quartz 5，只是想更新到最新版本，请参见 [[upgrading|升级 Quartz]]。

如果你完全是 Quartz 新手，请跳过本指南，改为按照 [[installation|安装指南]] 操作。

## 开始之前：保存你的内容

在切换分支之前，请确保你的内容是安全的。切换到 v5 会用 v5 代码库替换你工作目录中的文件，因此在恢复之前，你的 v4 内容文件夹将不可见。

切换之前，请把你的内容文件夹复制到仓库之外的某个位置：

```bash
# macOS / Linux
cp -r content /tmp/quartz-content

# Windows (PowerShell)
Copy-Item -Recurse content $env:TEMP\quartz-content
```

> [!note] 你的旧分支会被保留
> 切换分支 **不会** 删除你的 v4（或 v3/hugo）分支。你随时可以用 `git checkout v4` 切回去，访问旧的内容和配置。

## 获取 v5 分支

无论你是从 Quartz 4 还是 Quartz 3 过来，第一步都是相同的：把 v5 分支取到你的机器上，并推送到你的仓库。

```bash
# 把官方 Quartz 仓库添加为名为 "upstream" 的远程（如果已设置可跳过）
git remote add upstream https://github.com/jackyzha0/quartz.git

# 从官方仓库获取 v5 分支
git fetch upstream v5

# 基于官方分支创建本地 v5 分支
git checkout -b v5 upstream/v5

# 安装依赖
npm i

# 把 v5 推送到你的 GitHub 仓库
git push -u origin v5
```

## 设置你的站点

切换到 v5 之后，运行交互式设置来配置站点并导入你的内容：

```bash
npx quartz create
```

它会提示你输入：

- 一个 **模板**（`default`、`obsidian`、`ttrpg`、`blog`）—— 选择最接近你原有设置的那一个。如果你使用 Obsidian 库，推荐 `obsidian`。
- 一个 **内容策略** —— 选择「Copy」，并指向你备份的内容文件夹。

如果你跳过了 `create` 向导，或需要手动恢复内容：

```bash
# macOS / Linux
cp -r /tmp/quartz-content/* content/

# Windows (PowerShell)
Copy-Item -Recurse $env:TEMP\quartz-content\* content\
```

运行 `create` 之后，安装生成的配置中引用的所有插件：

```bash
npx quartz plugin install --from-config
```

## v5 中的变化

Quartz 5 引入了社区插件系统，从根本上改变了插件与组件的管理方式。Quartz 4 中内置的大多数插件现在是独立的社区插件，由 [quartz-community](https://github.com/quartz-community) 组织维护。

主要变化：

- **配置格式**：TypeScript（`quartz.config.ts`、`quartz.layout.ts`）→ YAML（`quartz.config.yaml`）
- **插件系统**：插件现在是独立的 Git 仓库，通过 `npx quartz plugin add` 安装
- **导入方式**：社区插件使用 `ExternalPlugin.X()`（来自 `.quartz/plugins`），而不是 `Plugin.X()`（来自 `./quartz/plugins`）
- **布局结构**：`quartz.layout.ts` 已移除 —— 布局位置现在是 `quartz.config.yaml` 中每个插件的属性
- **页面类型**：用于页面渲染的新插件类别（内容页、文件夹页、标签页）
- **URL 大小写**：所有生成的 URL 现在都会转为小写并使用连字符（例如 `My Notes/Hello World.md` → `/my-notes/hello-world`）。在 v4 中，URL 会保留文件和文件夹名称的原始大小写。

### URL 大小写与 SEO

如果你的 v4 站点有包含大写字母的 URL，升级到 v5 后这些 URL 会返回 404 错误。这也会影响搜索引擎索引，因为 Google 将 URL 视为[区分大小写](https://developers.google.com/search/docs/crawling-indexing/url-structure)。

[[AliasRedirects]] 插件（默认启用）会自动处理这种情况。构建期间，它会检测原始路径包含大写字符的文件，并在旧 URL 处生成重定向页面。这些重定向页面包含恰当的 SEO 信号（`<link rel="canonical">`、`<meta http-equiv="refresh">`、`<meta name="robots" content="noindex">`），以便搜索引擎把排名转移到新的小写 URL。

无需手动配置 —— 插件默认启用，大小写重定向行为默认开启。如果你想禁用它，请在插件选项中设置 `enableCaseRedirects: false`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/alias-redirects
    enabled: true
    options:
      enableCaseRedirects: false
```

> [!tip] 托管在 Netlify？
> Netlify 会自动把所有 URL 转为小写，并发出服务器端的 301 重定向。如果你托管在 Netlify 上，大小写重定向页面并非严格必需，但留着也无妨。

> [!note] 大多数用户不必担心这些细节
> 如果你使用默认的 Quartz 4 配置（或只改动了 `npx quartz create` 会提示的设置项），设置向导会处理一切。下面的细节面向那些自定义过插件配置的用户。

### 插件对照表

v4 插件名与 v5 对应项的映射：

| v4 | v5 | 类型 |
| --- | --- | --- |
| `Plugin.FrontMatter()` | `ExternalPlugin.NoteProperties()` | 社区 |
| `Plugin.CreatedModifiedDate()` | `ExternalPlugin.CreatedModifiedDate()` | 社区 |
| `Plugin.SyntaxHighlighting()` | `ExternalPlugin.SyntaxHighlighting()` | 社区 |
| `Plugin.ObsidianFlavoredMarkdown()` | `ExternalPlugin.ObsidianFlavoredMarkdown()` | 社区 |
| `Plugin.GitHubFlavoredMarkdown()` | `ExternalPlugin.GitHubFlavoredMarkdown()` | 社区 |
| `Plugin.CrawlLinks()` | `ExternalPlugin.CrawlLinks()` | 社区 |
| `Plugin.Description()` | `ExternalPlugin.Description()` | 社区 |
| `Plugin.Latex()` | `ExternalPlugin.Latex()` | 社区 |
| `Plugin.RemoveDrafts()` | `ExternalPlugin.RemoveDrafts()` | 社区 |
| `Plugin.ContentPage()` | `ExternalPlugin.ContentPage()` | 社区（pageTypes） |
| `Plugin.FolderPage()` | `ExternalPlugin.FolderPage()` | 社区（pageTypes） |
| `Plugin.TagPage()` | `ExternalPlugin.TagPage()` | 社区（pageTypes） |
| `Plugin.NotFoundPage()` | `Plugin.PageTypes.NotFoundPageType()` | 内置（pageTypes） |
| `Plugin.ComponentResources()` | `Plugin.ComponentResources()`（未变） | 内置 |
| `Plugin.Assets()` | `Plugin.Assets()`（未变） | 内置 |
| `Plugin.Static()` | `Plugin.Static()`（未变） | 内置 |
| `Plugin.AliasRedirects()` | `ExternalPlugin.AliasRedirects()` | 社区 |
| `Plugin.ContentIndex()` | `ExternalPlugin.ContentIndex()` | 社区 |

组件布局映射：

| v4 布局 | v5 布局 |
| --- | --- |
| `Component.Explorer()` | `Plugin.Explorer()` |
| `Component.Graph()` | `Plugin.Graph()` |
| `Component.Search()` | `Plugin.Search()` |
| `Component.Backlinks()` | `Plugin.Backlinks()` |
| `Component.Darkmode()` | `Plugin.Darkmode()` |
| `Component.Footer()` | `Plugin.Footer()` |
| `Component.TableOfContents()` | `Plugin.TableOfContents()` |
| `Component.Head()` | `Component.Head()`（未变，内置） |
| `Component.Spacer()` | `Plugin.Spacer()` |

## 更新你的 CI/CD

Quartz 5 要求在构建之前先安装插件。请在你的 CI 流水线中添加插件安装步骤，并（可选地）添加缓存。

以下是推荐的做法，基于项目自身的 GitHub Actions：

```yaml
- name: Cache dependencies
  uses: actions/cache@v5
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-

- name: Cache Quartz plugins
  uses: actions/cache@v5
  with:
    path: .quartz/plugins
    key: ${{ runner.os }}-plugins-${{ hashFiles('quartz.lock.json') }}
    restore-keys: |
      ${{ runner.os }}-plugins-

- run: npm ci

- name: Install Quartz plugins
  run: npx quartz plugin install

- name: Build Quartz
  run: npx quartz build
```

插件缓存使用 `quartz.lock.json` 作为缓存键，因此只有当锁文件发生变化时才会重新下载插件。

对于非 GitHub 的 CI 提供商（Cloudflare、Vercel、Netlify），构建命令应为：

```shell
npx quartz plugin install && npx quartz build
```

各提供商的具体设置细节见 [[hosting]]。

## 将默认分支设为 v5

在确认站点能正确构建和部署之后，把仓库的默认分支更新为 `v5`：

1. 在 GitHub 上打开你的仓库
2. 进入 **Settings** → **General**
3. 在 **Default branch** 下，点击当前默认分支旁边的切换图标
4. 从下拉列表中选择 `v5`，然后点击 **Update**
5. 确认更改

这样可以确保新的克隆、拉取请求和 GitHub Pages 部署默认都针对 v5。你的旧 v4 分支仍然保留，可供查阅。

> [!warning] 更新你的 CI 触发条件
> 如果你的 CI 工作流在特定分支上触发（例如 `branches: [v4]`），请务必把它更新为 `v5`。示例见 [[hosting]] 指南。

## 面向 Quartz 3 用户的说明

如果你是从 Quartz 3（基于 Hugo 的版本）过来，请按照上面相同的步骤操作 —— 获取 v5 分支、运行 `npx quartz create`，并导入你的内容。无需先经过 Quartz 4。

### 相对 Quartz 3 的主要变化

1. **Hugo 已移除**：Quartz 现在使用基于 Node 的静态站点生成流程。不再需要 Go 模板或 `hugo-obsidian`。
2. **完整的热重载**：开发服务器（`npx quartz build --serve`）会在每次更改时重新处理所有内容。
3. **用 JSX 替代 Go 模板**：布局组件用 JSX（JavaScript XML）编写，自定义起来要容易得多。
4. **新的插件系统**：可扩展插件架构的细节见 [[configuration#Plugins|插件]]。

### 需要更新的事项

- 更新你的部署脚本 —— 见 [[hosting]] 指南。
- 确保 GitHub 上的默认分支已更新为 `v5`。
- [[folder and tag listings|文件夹与标签列表]] 已发生变化：
  - 文件夹描述放在 `content/<folder-name>/index.md` 下
  - 标签描述放在 `content/tags/<tag-name>.md` 下
- 如果你依赖 Quartz 3 中特定的 HTML 层级或类名，可能需要更新自定义 CSS。
