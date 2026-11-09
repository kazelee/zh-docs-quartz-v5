---
date: 2026-09-20
title: 加密页面 (EncryptedPages)
description: 受密码保护的加密页面，配有影子内容索引。
tags:
  - plugin/transformer
  - plugin/emitter
image: "#FF1493"
new-in-v5: true
repository: "[quartz-community/encrypted-pages](https://github.com/quartz-community/encrypted-pages)"
enabled: true
required: false
---

受密码保护的加密页面。在构建时使用 AES-256-GCM 加密页面内容，并在客户端使用 Web Crypto API 解密。密码通过 frontmatter 按页面设置。配套的输出器会写入一个加密的影子内容索引，使未列出的加密页面在成功解密后可以动态出现在关系图谱、资源管理器和搜索中——同时绝不会向不持有密码的访客泄露其元数据。

> [!example] 实时演示
> 自己试一试：[[EncryptedPages Demo]]。密码是 `quartz`。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#插件|配置]] 页面。

## 用法

在任意页面的 frontmatter 中添加 `password` 字段即可加密该页面：

```yaml
---
title: My Secret Page
password: mysecretpassword
---
```

页面内容将在构建时被加密。访客必须输入正确的密码才能查看内容。

验证成功的密码会被缓存在浏览器的会话存储中，并会自动在其他加密页面上尝试，以方便使用。

### 从发现入口隐藏加密页面

默认情况下，加密页面仍会出现在关系图谱、资源管理器、搜索、RSS、站点地图和反向链接中——访客可以看到该页面存在及其标题，但没有密码就无法阅读内容。

若要在访客成功解密之前完全隐藏某个加密页面，请在其 frontmatter 中设置 `unlisted: true`：

```yaml
---
title: My Secret Page
password: mysecretpassword
unlisted: true
---
```

未列出的页面：

- **不会出现**在 `contentIndex.json`、`sitemap.xml`、RSS 订阅源、反向链接、最近笔记、文件夹列表、标签列表、bases 视图、关系图谱、资源管理器和搜索中。
- 仍会输出为 HTML，因此依然可以通过直接 URL 访问。
- 其元数据（slug、标题、链接、标签）会被写入单独的 `static/encryptedContentIndex.json` 文件，并使用该页面自己的密码加密。
- 当访客成功解密后，它会动态重新加入内存中的内容索引，因此关系图谱、资源管理器和搜索会在本次浏览器会话的剩余时间内反映它。服务端渲染的列表——反向链接、最近笔记、标签页面、文件夹列表和 [[BasesPage|bases 视图]]——即使在解密后仍保持静态隐藏，因为它们在构建时就被烘焙为 HTML。

若要让这成为站点上每个加密页面的默认行为，请在插件选项中设置 `unlistWhenEncrypted: true`。之后单个页面可以用 `unlisted: false` 重新选择显示。

> [!note]
> 上面提到的 `unlisted: true` frontmatter 字段只有在安装了此插件时才对加密页面生效。如果你还希望 `unlisted: true` 在站点中的**非加密**页面上也生效，请同时安装 [[UnlistedPages]]。这两个插件可以很好地配合——当两者都启用时，`unlisted: true` 会将任何页面（无论是否加密）从所有遵循 `file.data.unlisted` 约定的发现入口中隐藏。

### 永久隐藏加密页面（`stealth`）

默认情况下，`unlisted: true` 的加密页面在访客成功解密后会在关系图谱、资源管理器和搜索中被*显示*出来。这通常正是你想要的效果：用户刚刚证明了自己知道密码，因此在本次会话的剩余时间里在侧边栏中向他们显示该页面是合理的。

如果你希望某个页面永久不可见——只能通过直接 URL 访问，即使对同一站点上已成功解密其他页面的用户也是如此——请在其 frontmatter 中设置 `stealth: true`：

```yaml
---
title: Deep Secret
password: mysecretpassword
stealth: true
---
```

stealth 页面：

- 与任何 `unlisted` 页面一样，**不会出现**在任何发现入口中。
- 在影子内容索引（`encryptedContentIndex.json`）中**没有条目**。插件在构建影子索引时会刻意跳过 stealth 页面。
- 即使访客输入了正确的密码，它仍保持隐藏。由于没有可解密的影子索引条目，也就没有内容可以修补到内存中的内容索引——关系图谱、资源管理器和搜索永远不会知道该页面的存在。用户在页面本身上只能看到已解密的 HTML。
- 密码仍会缓存在会话存储中，因此再次访问同一个 stealth 页面时会自动解锁。

`stealth: true` 隐含了 `unlisted: true`——你不需要同时设置两者，而且如果你写成 `stealth: true, unlisted: false`，stealth 标志会胜出。在非加密页面上，`stealth: true` 不起作用（没有可跳过的影子索引）。

对于只应触达已经知道确切 URL 的用户的「暗门」内容，可以使用 stealth 页面：例如从外部 wiki 链接过来的私人笔记、你发给特定人员的个人页面，或者任何你绝不想让它出现在站点内部搜索中（即使对已认证读者）的内容。

## 配置

此插件提供一个转换器、一个输出器和一个组件。所有选项都在单个配置条目上设置，并由转换器和输出器共享——Quartz 会自动实例化两者。

- `iterations`：用于密钥派生的 PBKDF2 迭代次数。值越高越安全，但解锁也越慢。默认为 `600000`。
- `passwordField`：存放页面密码的 frontmatter 字段名。由转换器和输出器共享。默认为 `"password"`。
- `unlistWhenEncrypted`：若为 `true`，每个加密页面都会被标记为 `unlisted`，除非其 frontmatter 显式覆盖。默认为 `false`。
- `outputPath`：影子内容索引的输出路径，相对于 Quartz 的输出目录。默认为 `"static/encryptedContentIndex.json"`。

### 组件选项

- `className`：组件外层容器的 CSS 类。默认为 `"encrypted-page-wrapper"`。

### 默认选项

```yaml title="quartz.config.yaml"
- source: github:quartz-community/encrypted-pages
  enabled: true
  options:
    iterations: 600000
    passwordField: password
    unlistWhenEncrypted: false
    outputPath: static/encryptedContentIndex.json
```

> [!warning]
> `EncryptedPages` 转换器会用不透明的密文容器替换加密页面的整个 HAST 树。任何需要读取真实 HTML 的转换器——特别是 [[CrawlLinks]]，它负责填充反向链接和影子内容索引所用的链接——都必须在 `EncryptedPages` **之前**运行。可以使用 `quartz.config.yaml` 中的 `order` 字段来控制这一点。

## 安全性

- 内容使用 AES-256-GCM 加密，密钥派生使用 PBKDF2 SHA-256。
- 无论可见性设置如何，明文都会从搜索索引、RSS 订阅源和影子内容索引中剔除。
- 影子内容索引是一个由不透明加密数据块组成的扁平数组。攻击者下载它后只能得知未列出加密页面的数量和 PBKDF2 迭代次数——不会泄露 slug、标题或链接关系。
- 密码按页面在 frontmatter 中设置。避免将密码提交到公开仓库。
- 这是针对静态站点的客户端加密。它可以防止随意浏览，但无法抵御能够访问页面源代码的坚定攻击者。

## API

- 类别：转换器、输出器
- 函数名：`ExternalPlugin.EncryptedPages()`、`ExternalPlugin.EncryptedContentIndex()`。
- 来源：[`quartz-community/encrypted-pages`](https://github.com/quartz-community/encrypted-pages)
- 安装：`npx quartz plugin add github:quartz-community/encrypted-pages`
