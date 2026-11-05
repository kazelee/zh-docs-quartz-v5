---
title: "Docker 支持"
tags:
  - feature
---

Quartz 附带了一个 Docker 镜像，让你无需安装 Node 即可在本地预览你的 Quartz。

你可以运行下面这行命令来在 Docker 中运行 Quartz。

```sh
docker run --rm -itp 8080:8080 -p 3001:3001 -v ./content:/usr/src/app/content $(docker build -q .)
```

> [!warning] 请勿用于生产环境
> Serve 模式仅用于本地预览。
> 生产环境的工作负载，请参阅 [[hosting]] 页面。
