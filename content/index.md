---
date: 2026-09-20
title: 欢迎使用 Quartz 5
---

> [TranslatorNote] 译者注：翻译该网站时，误以为所有的文档日期都是 `2026-09-20`，但实际可能不是这样。同时，由于翻译修复的过程中，原网站又进行了更新，实际的日期已经不可考证，故全部改为主页的日期。本翻译仅供个人自用参考，故不会同步更新官网进度，还请谅解。其他文档的翻译笔记内容，可以尝试全局搜索 `[TranslatorNote]` 查询（不要直接使用 `译者注` 查询，该网页的全局搜索使用中文时存在一定问题，可能会命中并不存在该关键词的页面）

Quartz 是一个快速、开箱即用的静态站点生成器，可将 Markdown 内容转换为功能完备的网站。数千名学生、开发者和教师正在 [[showcase|使用 Quartz]] 将个人笔记、网站和[数字花园](https://jzhao.xyz/posts/networked-thought)发布到网络上。

## 🪴 快速开始

Quartz 需要 **至少 [Node](https://nodejs.org/) v22** 和 `npm` v10.9.2 才能正常运行。继续操作之前，请确保你的机器上已安装这些内容。安装方法参见[[getting-started/index#前置条件|前置条件]]。

> [!tip] GitHub 用户
> 你也可以使用 **[GitHub 模板](https://github.com/jackyzha0/quartz/generate)** 一键创建你的仓库，然后改为克隆该仓库。参见安装指南中的[[installation#方式 A：使用 GitHub 模板（推荐）|选项 A]]。

```shell
# 1. 克隆 Quartz 仓库
git clone https://github.com/jackyzha0/quartz.git
cd quartz

# 2. 安装依赖
npm i

# 3. 初始化你的站点（选择模板、设置 baseUrl、导入内容）
npx quartz create

# 4. 安装所选模板引用的插件
npx quartz plugin install --from-config

# 5. 在本地预览你的站点
npx quartz build --serve
```

你的站点现在运行在 `http://localhost:8080`。接下来你可以：

- **在 `content/` 文件夹中[[authoring-content|编写内容]]**
- **使用 `npx quartz sync` [[installation|推送到 GitHub]]**
- **[[hosting|部署]]到 GitHub Pages、Cloudflare、Netlify 或 Vercel**

完整教程参见[[getting-started/index|快速开始]]指南。

### 老用户？

已经有一个 Quartz 仓库，想在新机器上克隆它？

```shell
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
npm ci
npx quartz plugin install
npx quartz build --serve
```

> [!tip]
> 如果在全新克隆后遇到构建错误，可以尝试 `npx quartz plugin install --latest` 将插件刷新到最新版本。详情参见 [[troubleshooting#全新克隆后插件构建失败]]。

## 🔧 功能

- 开箱即用的 [[Obsidian compatibility]]、[[full-text search]]、[[graph view]]、[[wikilinks|维基链接、嵌入]]、[[plugins/Backlinks]]、[[features/Latex|Latex]]、[[syntax highlighting]]、[[popover previews]]、[[Docker Support]]、[[i18n|国际化]]、[[features/comments|评论]]以及[更多功能](./features/)
- 配置修改支持热重载，内容修改支持增量重建
- 简洁的 JSX 布局与[[creating components|页面组件]]
- [[SPA Routing|极快的页面加载速度]]与极小的打包体积
- 通过[[making plugins|插件]]完全自定义解析、过滤与页面生成

完整的特性列表参见[特性页面](./features/)。你可以在[[philosophy]]页面进一步了解这些特性背后的 _原因_，并在[[architecture]]页面查看技术概览。

### 🚧 故障排查与更新

使用 Quartz 遇到问题？可以尝试用搜索功能查找你的问题，或查看[[troubleshooting]]页面。如果你还没有升级，请[[upgrading|升级]]到最新版本的 Quartz，看看能否解决你的问题。

如果仍有问题，认为发现了 bug，可以随时[提交 issue](https://github.com/jackyzha0/quartz/issues)，或在我们的 [Discord 社区](https://discord.gg/cRFFHYye7t)寻求帮助。你也可以浏览[[community]]页面，了解第三方插件和资源。
