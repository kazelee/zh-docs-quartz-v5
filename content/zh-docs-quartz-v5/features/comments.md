---
title: 评论
tags:
  - component
---

Quartz 还可以接入多种提供方，让读者在你的站点上留下评论。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

![[giscus-example.png]]

截至目前，开箱即用的只有 [Giscus](https://giscus.app/)，但欢迎提交 PR 来支持其他提供方！

## 提供方

### Giscus

首先，请确保你用于 Quartz 的 [[setting up your GitHub repository|GitHub]] 仓库满足以下要求：

1. **仓库为[公开](https://docs.github.com/en/github/administering-a-repository/managing-repository-settings/setting-repository-visibility#making-a-repository-public)状态**，否则访客将无法查看讨论。
2. **已安装 [giscus](https://github.com/apps/giscus) 应用**，否则访客将无法评论和表态。
3. **已开启 Discussions 功能**，方法是[为你的仓库启用该功能](https://docs.github.com/en/github/administering-a-repository/managing-repository-settings/enabling-or-disabling-github-discussions-for-a-repository)。

然后，使用 [Giscus 站点](https://giscus.app/#repository) 来确定你的 `repoId` 和 `categoryId` 应该是什么。请确保将 Discussion 类别选择为 `Announcements`。

![[giscus-repo.png]]

![[giscus-discussion.png]]

在输入仓库并选择讨论类别之后，Giscus 会计算出一些 ID，你需要把它们填回 Quartz。你不需要自己手动添加脚本，Quartz 会为你处理这部分，但下一步你会用到这些值！

![[giscus-results.png]]

最后，在 `quartz.config.yaml` 中使用以下选项添加 comments 插件（使用你从上面得到的值）：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/comments
    enabled: true
    options:
      provider: giscus
      options:
        repo: jackyzha0/quartz
        repoId: MDEwOlJlcG9zaXRvcnkzODcyMTMyMDg
        category: Announcements
        categoryId: DIC_kwDOFxRnmM4B-Xg6
        lang: en
    layout:
      position: afterBody
      priority: 10
```

对于 TS 覆盖的方式：

```ts title="quartz.ts (override)"
// 如果使用 quartz.ts 覆盖而不是 YAML：
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"

const config = await loadQuartzConfig()
export default config
export const layout = await loadQuartzLayout({
  defaults: {
    afterBody: [
      ExternalPlugin.Comments({
        provider: "giscus",
        options: {
          repo: "jackyzha0/quartz",
          repoId: "MDEwOlJlcG9zaXRvcnkzODcyMTMyMDg",
          category: "Announcements",
          categoryId: "DIC_kwDOFxRnmM4B-Xg6",
          lang: "en",
        },
      }),
    ],
  },
})
```

> [!note]
> 请先安装 comments 插件：`npx quartz plugin add github:quartz-community/comments`

### 自定义

Quartz 还暴露了其他一些 Giscus 选项，你可以用与提供 `repo`、`repoId`、`category` 和 `categoryId` 相同的方式来提供它们。

```ts
type Options = {
  provider: "giscus"
  options: {
    repo: `${string}/${string}`
    repoId: string
    category: string
    categoryId: string

    // 存放自定义主题的文件夹 URL
    // 默认为 'https://${cfg.baseUrl}/static/giscus'
    themeUrl?: string

    // 浅色主题 .css 文件的文件名
    // 默认为 'light'
    lightTheme?: string

    // 深色主题 .css 文件的文件名
    // 默认为 'dark'
    darkTheme?: string

    // 如何将页面映射到讨论
    // 默认为 'url'
    mapping?: "url" | "title" | "og:title" | "specific" | "number" | "pathname"

    // 是否使用严格的标题匹配
    // 默认为 true
    strict?: boolean

    // 是否为主题帖启用表态
    // 默认为 true
    reactionsEnabled?: boolean

    // 评论输入框相对于评论的位置
    // 默认为 'bottom'
    inputPosition?: "top" | "bottom"

    // 在此设置你的首选语言
    // 默认为 'en'
    lang?: string
  }
}
```

#### 自定义 CSS 主题

Quartz 支持为 Giscus 使用自定义主题。要使用自定义 CSS 主题，请将 `.css` 文件放在 `quartz/static` 文件夹中，并设置相应的配置值。

例如，如果你有一个浅色主题 `light-theme.css`、一个深色主题 `dark-theme.css`，并且你的 Quartz 站点托管在 `https://example.com/`：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/comments
    enabled: true
    options:
      provider: giscus
      options:
        # 其他选项...
        themeUrl: "https://example.com/static/giscus" # 对应 quartz/static/giscus/
        lightTheme: light-theme # 对应 quartz/static/giscus/ 中的 light-theme.css
        darkTheme: dark-theme # 对应 quartz/static/giscus/ 中的 dark-theme.css
```

```ts title="quartz.ts (override)"
import { loadQuartzConfig, loadQuartzLayout } from "./quartz/plugins/loader/config-loader"

const config = await loadQuartzConfig()
export default config
export const layout = await loadQuartzLayout({
  defaults: {
    afterBody: [
      ExternalPlugin.Comments({
        provider: "giscus",
        options: {
          // 其他选项...
          themeUrl: "https://example.com/static/giscus",
          lightTheme: "light-theme",
          darkTheme: "dark-theme",
        },
      }),
    ],
  },
})
```

#### 按条件显示评论

Quartz 可以根据 frontmatter 中的 `comments` 字段有条件地显示评论框。默认情况下，所有页面都会显示评论；要针对特定页面禁用，请将 `comments` 设置为 `false`。

```
---
title: Comments disabled here!
comments: false
---
```
