---
title: quartz build
aliases:
  - build
---

`build` 命令将你的 Markdown 内容转换为静态 HTML 网站。它通过已配置的插件处理你的文件，并将最终站点输出到你选择的目录。

## 标志

| 标志              | 简写      | 说明                                                      | 默认值         |
| ----------------- | --------- | --------------------------------------------------------- | -------------- |
| `--directory`     | `-d`      | 包含你的 Quartz 项目的目录                                | 当前目录       |
| `--verbose`       | `-v`      | 启用详细的日志记录以便调试                                | `false`        |
| `--output`        | `-o`      | 构建后的站点保存到的目录                                  | `public`       |
| `--serve`         |           | 启动本地开发服务器                                        | `false`        |
| `--watch`         |           | 文件变化时重新构建站点                                    | `false`        |
| `--port`          |           | 开发服务器的端口                                          | `8080`         |
| `--wsPort`        |           | WebSocket 热重载服务器的端口                              | `3001`         |
| `--baseDir`       |           | 为站点设置基础目录（例如用于 GitHub Pages）               | `/`            |
| `--remoteDevHost` |           | 开发服务器使用的主机名                                    | `localhost`    |
| `--bundleInfo`    |           | 输出一个包含打包体积信息的 JSON 文件                      | `false`        |
| `--concurrency`   | `-c`      | 构建时使用的工作线程数                                    | CPU 核心数     |

## 示例

### 基本构建

将站点生成到 `public` 文件夹中。

```shell
npx quartz build
```

### 开发模式

启动本地服务器并监听文件变化。这是在写作时预览站点最常用的方式。

```shell
npx quartz build --serve
```

### 自定义输出与端口

构建到指定文件夹，并在不同端口上运行服务器。

```shell
npx quartz build --serve --output dist --port 3000
```

### 性能调优

如果你的库非常大，可以限制并发工作线程数来节省内存。

```shell
npx quartz build --concurrency 2
```

## Serve 与 Watch

`--serve` 和 `--watch` 标志控制不同的行为：

- **`--serve`** 启动本地开发服务器，并自动监听文件变化（隐含 `--watch`）。这是本地开发推荐使用的模式。
- **`--watch`** 只监听文件变化并自动重新构建，不启动服务器。这适用于 CI 流水线或自定义服务器配置：你希望自动重新构建，但自行负责提供服务。

大多数情况下，你需要使用 `--serve`：

```shell
npx quartz build --serve
```

## 开发服务器

`--serve` 标志会启动本地 Web 服务器。该服务器仅用于开发和预览，不适合用于生产环境。有关如何部署站点的信息，见 [[hosting]]。

### 热重载

使用 `--serve` 运行时，Quartz 会自动启用 `--watch`。它使用 WebSocket 连接（端口由 `--wsPort` 指定）在文件发生变化时通知你的浏览器。随后浏览器会自动刷新，以显示你内容的最新版本。
