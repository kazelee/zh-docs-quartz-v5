Quartz 会为站点上的所有内容输出 RSS feed，方式是生成一个可供 RSS 阅读器订阅的 `index.xml` 文件。由于 RSS 规范的要求，这需要你的 [[configuration]] 中的 `baseUrl` 属性设置正确，RSS 阅读器才能正确获取。

> [!info]
> 部署后，生成的 RSS 链接默认位于 `https://${baseUrl}/index.xml`。
>
> 可以通过向 [[ContentIndex]] 插件传递 `rssSlug` 选项来自定义 `index.xml` 路径。

## 配置

此功能由 [[ContentIndex]] 插件提供。有关自定义选项，请参阅该插件页面。
