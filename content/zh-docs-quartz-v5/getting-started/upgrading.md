---
title: "升级 Quartz"
aliases:
  - upgrading
---

> [!note]
> 本指南专门用于把你的 Quartz 升级到较新的版本。如果你是从 Quartz 4 或 Quartz 3 过来，请查阅 [[migrating|迁移指南]] 了解更多信息。

要获取最新的 Quartz 更新，只需运行

```bash
npx quartz upgrade
```

由于 Quartz 底层使用 [git](https://git-scm.com/) 进行版本管理，升级实际上是从官方 Quartz GitHub 仓库「拉取」更新。`quartz.lock.json` 中的合并冲突会自动处理 —— Quartz 会在拉取前备份你的锁文件，并在拉取后恢复它。对于其他因本地修改而与更新冲突的文件，你可能需要自行手动解决（或者使用 `git pull origin upstream` 手动拉取）。

> [!hint]
> Quartz 会在升级前尝试缓存你的内容，以尽量避免合并冲突。如果你在合并过程中遇到冲突，可以停止合并，然后运行 `npx quartz restore` 从缓存恢复内容。

如果你安装了 [GitHub desktop 应用](https://desktop.github.com/)，它会自动打开以帮助你解决冲突。否则，你需要在 VSCode 之类的文本编辑器中解决。关于手动解决冲突的更多帮助，请查阅 [GitHub 关于解决合并冲突的指南](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line#competing-line-change-merge-conflicts)。

要单独更新已安装的插件，请使用：

```bash
npx quartz plugin install --latest
```

可用标志的更多细节见 [[upgrade|升级的 CLI 参考]]。

### 布局系统的变化

`footer` 布局槽现在是一个组件数组，与其他布局槽（如 `header`、`left` 和 `right`）保持一致。此外，`header` 和 `footer` 现在是可配置的布局位置 —— 插件可以在其 YAML 配置中声明 `layout: { position: header }` 或 `layout: { position: footer }`。

**如果你在 `quartz.ts` 中覆盖了布局**，请把所有 `footer` 赋值改为使用数组：

```ts title="quartz.ts"
// 修改前
export const layout = await loadQuartzLayout({
  defaults: { footer: MyFooterComponent },
})

// 修改后
export const layout = await loadQuartzLayout({
  defaults: { footer: [MyFooterComponent] },
})
```

**如果你有自定义页面框架**，请更新 `render` 函数以遍历 footer 数组：

```tsx
// 修改前
render({ footer: Footer, ...rest }: PageFrameProps) {
  return <Footer {...componentData} />
}

// 修改后
render({ footer, ...rest }: PageFrameProps) {
  return footer.map((F) => <F {...componentData} />)
}
```

这两处改动都会在编译时被 TypeScript 捕获 —— 运行 `npx quartz build` 就会显示错误。

`quartz.config.yaml` 无需改动 —— YAML 格式没有变化。

### 清理未使用的插件

如果你在升级过程中从配置中移除了插件，可以清理遗留的文件：

```bash
npx quartz plugin prune --dry-run  # 预览将被移除的内容
npx quartz plugin prune            # 移除孤立的插件
```

更多细节见 [[cli/plugin#prune|plugin prune 参考]]。

## 切换到 npm 插件说明符

Quartz 官方插件现在以 `@quartz-community` 作用域发布到 npm。如果你的 `quartz.config.yaml` 使用 `github:` 说明符，可以选择切换到 npm 说明符，以获得更快的安装速度和更好的版本锁定。

### 为什么要切换？

- **更快的安装**：npm 包会缓存在本地，无需 git 克隆
- **版本锁定**：npm 使用 semver 范围，因此你可以控制何时更新
- **无需构建步骤**：npm 包是预先构建好的，而 git 源可能需要在安装时构建

### 如何迁移

把 `quartz.config.yaml` 中每个插件的 source 从 `github:` 格式改为带引号的 npm 格式：

```yaml
# 修改前
plugins:
  - source: github:quartz-community/syntax-highlighting
    enabled: true

# 修改后
plugins:
  - source: "@quartz-community/syntax-highlighting"
    enabled: true
```

> [!important]
> `@` 作用域的名称在 YAML 中必须加引号。请用双引号包裹 source 的值。

然后安装这些包：

```bash
npm install @quartz-community/syntax-highlighting
```

或者一次性安装所有默认插件：

```bash
npm install @quartz-community/created-modified-date @quartz-community/syntax-highlighting @quartz-community/obsidian-flavored-markdown @quartz-community/github-flavored-markdown @quartz-community/table-of-contents @quartz-community/crawl-links @quartz-community/description @quartz-community/latex @quartz-community/quartz-fonts @quartz-community/remove-draft @quartz-community/alias-redirects @quartz-community/content-index @quartz-community/favicon @quartz-community/og-image @quartz-community/cname @quartz-community/canvas-page @quartz-community/content-page @quartz-community/folder-page @quartz-community/tag-page @quartz-community/explorer @quartz-community/graph @quartz-community/search @quartz-community/backlinks @quartz-community/article-title @quartz-community/content-meta @quartz-community/page-title @quartz-community/darkmode @quartz-community/reader-mode @quartz-community/breadcrumbs @quartz-community/footer @quartz-community/spacer @quartz-community/bases-page @quartz-community/note-properties @quartz-community/unlisted-pages @quartz-community/encrypted-pages
```

### 必须切换吗？

不是必须的。`github:` 说明符继续可用，并将无限期得到支持。对于未发布到 npm 的第三方和社区插件，它仍然是推荐做法。npm 方式对官方插件来说是一个可选的改进。
