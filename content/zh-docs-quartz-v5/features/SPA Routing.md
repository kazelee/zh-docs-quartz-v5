---
title: "SPA 路由"
tags:
  - feature
---

单页应用风格的渲染。这可以防止出现无样式内容闪烁，并提升 Quartz 的流畅度。

在底层，这是通过接管页面导航，改用 `GET` 请求获取 HTML，然后使用 [micromorph](https://github.com/natemoo-re/micromorph) 对比并选择性地替换页面的部分内容来实现的。这让我们可以在不完全刷新页面的情况下更改页面内容，减少浏览器需要加载的内容量。

## 配置

- 禁用 SPA 路由：将 `quartz.config.yaml` 中 [[configuration]] 的 `enableSPA` 字段设置为 `false`。
