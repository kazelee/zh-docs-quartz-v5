---
title: CNAME
description: 为自定义域名部署输出 CNAME 文件。
tags:
  - plugin/emitter
image:
repository: "[quartz-community/cname](https://github.com/quartz-community/cname)"
enabled: true
required: false
---

该插件输出一条 `CNAME` 记录，将你的子域指向站点的默认域名。

如果你想为站点使用像 `quartz.example.com` 这样的自定义域名，就需要它。

更多信息请参阅 [[hosting|托管]]。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参阅 [[configuration#Plugins|配置]] 页面。

该插件没有配置选项。

## API

- 类别：输出器
- 函数名：`ExternalPlugin.CNAME()`。
- 来源：[`quartz-community/cname`](https://github.com/quartz-community/cname)
- 安装：`npx quartz plugin add github:quartz-community/cname`
