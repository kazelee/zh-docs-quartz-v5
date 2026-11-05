---
title: 自定义 OG 图片 (CustomOgImages)
description: 生成 Open Graph 社交预览图片。
tags:
  - feature/emitter
image: "[[social-image-preview-dark.png]]"
repository: "[quartz-community/og-image](https://github.com/quartz-community/og-image)"
enabled: true
required: false
---

Custom OG Images 输出器插件为你的页面生成社交媒体预览图片。它使用 [satori](https://github.com/vercel/satori) 将 HTML/CSS 转换为图片，让你可以为内容创建美观且一致的社交媒体预览卡片。

> [!note]
> 有关如何添加、移除或配置插件的信息，请参见 [[configuration#Plugins|配置]] 页面。

## 功能

- 为每个页面自动生成社交媒体预览图片
- 同时支持浅色和深色模式主题
- 可通过 frontmatter 属性自定义
- 在需要时回退到默认图片
- 通过自定义组件完全控制图片设计

## 配置

> [!info] 信息
>
> 必须正确设置 [[configuration]] 中的 `baseUrl` 属性，社交图片才能正常工作，因为它们需要绝对路径。

本插件接受以下配置选项：

```yaml title="quartz.config.yaml"
plugins:
  - source: github:quartz-community/og-image
    enabled: true
    options:
      colorScheme: lightMode # "lightMode" 或 "darkMode"
      width: 1200
      height: 630
      excludeRoot: false
```

对于 TS 覆盖方式（自定义 `imageStructure` 时需要）：

```ts title="quartz.ts (override)"
import * as ExternalPlugin from "./.quartz/plugins"
import { defaultImage } from "./quartz/plugins/emitters/ogImage"

// 必须放在 loadQuartzConfig() 之前
ExternalPlugin.CustomOgImages({
  colorScheme: "lightMode",
  width: 1200,
  height: 630,
  excludeRoot: false,
  imageStructure: defaultImage,
})
```

### 配置选项

| 选项 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `colorScheme` | string | "lightMode" | 用于生成图片的主题（"darkMode" 或 "lightMode"） |
| `width` | number | 1200 | 生成图片的宽度，单位为像素 |
| `height` | number | 630 | 生成图片的高度，单位为像素 |
| `excludeRoot` | boolean | false | 是否将根索引页面排除在自动生成的图片之外 |
| `defaultTitle` | string | "Untitled" | 页面没有标题时使用的回退标题 |
| `defaultDescription` | string | "No description provided" | 页面没有描述时使用的回退描述 |
| `imageStructure` | component | defaultImage | 用于生成图片的自定义组件 |

## frontmatter 属性

以下属性可用于自定义链接预览：

| 属性 | 别名 | 摘要 |
| --- | --- | --- |
| `socialDescription` | `description` | 用于预览的描述。 |
| `socialImage` | `image`, `cover` | 预览图片的链接。 |

`socialImage` 属性应包含图片的链接，可以是相对于 `quartz/static` 的路径，也可以是完整 URL。如果你把所有图片都放在 `quartz/static/my-images` 文件夹中，那么 `socialImage` 的一个示例可以是 `"my-images/cover.png"`。你也可以使用类似 `"https://example.com/cover.png"` 的完整 URL。

> [!info] 信息
>
> 封面图片的选用优先级如下：`frontmatter property > generated image (if enabled) > default image`。
>
> 默认图片（`quartz/static/og-image.png`）仅在未设置其他图片时作为回退使用。如果启用了 Custom OG Images 输出器插件，它会被视为每个页面新的默认图片，但可以通过为该页面设置 `socialImage` frontmatter 属性来覆盖。

## 自定义

你可以将自己的组件传给 `imageStructure`，从而完全自定义所生成图片的外观。该组件接收 JSX 以及一些页面元数据/配置选项，并使用 [satori](https://github.com/vercel/satori) 将其转换为图片。Vercel 提供了一个[在线演练场](https://og-playground.vercel.app/)，可用于预览 JSX 渲染成图片的效果。这非常适合为你的自定义设计做原型。

### 字体

你还会收到一个包含标题字体和正文字体的数组（第一项是标题字体，第二项是正文字体）。这些字体与 `quartz.config.yaml` 中 `theme.typography.header` 和 `theme.typography.body` 所选的字体一致，并按 [`satori`](https://github.com/vercel/satori) 所需的格式传入。要在 CSS 中使用它们，请使用 `.name` 属性（例如 `fontFamily: fonts[1].name` 可改用「body」字体族）。

使用标题字体的组件示例可能如下所示：

```tsx title="socialImage.tsx"
export const myImage: SocialImageOptions["imageStructure"] = (...) => {
  return <p style={{ fontFamily: fonts[0].name }}>Cool Header!</p>
}
```

> [!example]- 本地字体
>
> 如果你在 `static` 文件夹下使用本地字体，请确保在 `custom.scss` 中设置正确的 `@font-face`：
>
> ```scss title="custom.scss"
> @font-face {
>   font-family: "Newsreader";
>   font-style: normal;
>   font-weight: normal;
>   font-display: swap;
>   src: url("/static/Newsreader.woff2") format("woff2");
> }
> ```
>
> 然后在 `quartz/util/og.tsx` 中，可以这样加载 Satori 字体：
>
> ```tsx title="quartz/util/og.tsx"
> import { joinSegments, QUARTZ } from "../path"
> import fs from "fs"
> import path from "path"
>
> const newsreaderFontPath = joinSegments(QUARTZ, "static", "Newsreader.woff2")
> export async function getSatoriFonts(headerFont: FontSpecification, bodyFont: FontSpecification) {
>   // ... 其余实现保持不变
>   const fonts: SatoriOptions["fonts"] = [
>     ...headerFontData.map((data, idx) => ({
>       name: headerFontName,
>       data,
>       weight: headerWeights[idx],
>       style: "normal" as const,
>     })),
>     ...bodyFontData.map((data, idx) => ({
>       name: bodyFontName,
>       data,
>       weight: bodyWeights[idx],
>       style: "normal" as const,
>     })),
>     {
>       name: "Newsreader",
>       data: await fs.promises.readFile(path.resolve(newsreaderFontPath)),
>       weight: 400,
>       style: "normal" as const,
>     },
>   ]
>
>   return fonts
> }
> ```
>
> 之后该字体就可以在你的自定义结构中使用。

## 示例

以下是一些示例图片组件，你可以把它们作为起点：

### 基础示例

该示例生成的图片如下所示：

| 浅色 | 深色 |
| --- | --- |
| ![[custom-social-image-preview-light.png]] | ![[custom-social-image-preview-dark.png]] |

```tsx
import { SatoriOptions } from "satori/wasm"
import { GlobalConfiguration } from "../cfg"
import { SocialImageOptions, UserOpts } from "./imageHelper"
import { QuartzPluginData } from "../plugins/vfile"

export const customImage: SocialImageOptions["imageStructure"] = (
  cfg: GlobalConfiguration,
  userOpts: UserOpts,
  title: string,
  description: string,
  fonts: SatoriOptions["fonts"],
  fileData: QuartzPluginData,
) => {
  // 在切换为更小字号之前允许的字符数
  const fontBreakPoint = 22
  const useSmallerFont = title.length > fontBreakPoint

  const { colorScheme } = userOpts
  return (
    <div
      style={{
        display: "flex",
        flexDirection: "row",
        justifyContent: "flex-start",
        alignItems: "center",
        height: "100%",
        width: "100%",
      }}
    >
      <div
        style={{
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          height: "100%",
          width: "100%",
          backgroundColor: cfg.theme.colors[colorScheme].light,
          flexDirection: "column",
          gap: "2.5rem",
          paddingTop: "2rem",
          paddingBottom: "2rem",
        }}
      >
        <p
          style={{
            color: cfg.theme.colors[colorScheme].dark,
            fontSize: useSmallerFont ? 70 : 82,
            marginLeft: "4rem",
            textAlign: "center",
            marginRight: "4rem",
            fontFamily: fonts[0].name,
          }}
        >
          {title}
        </p>
        <p
          style={{
            color: cfg.theme.colors[colorScheme].dark,
            fontSize: 44,
            marginLeft: "8rem",
            marginRight: "8rem",
            lineClamp: 3,
            fontFamily: fonts[1].name,
          }}
        >
          {description}
        </p>
      </div>
      <div
        style={{
          height: "100%",
          width: "2vw",
          position: "absolute",
          backgroundColor: cfg.theme.colors[colorScheme].tertiary,
          opacity: 0.85,
        }}
      />
    </div>
  )
}
```

### 进阶示例

以下示例包含一个带自定义背景和格式化日期的定制社交图片：

```typescript title="custom-og.tsx"
export const og: SocialImageOptions["Component"] = (
  cfg: GlobalConfiguration,
  fileData: QuartzPluginData,
  { colorScheme }: Options,
  title: string,
  description: string,
  fonts: SatoriOptions["fonts"],
) => {
  let created: string | undefined
  let reading: string | undefined
  if (fileData.dates) {
    created = formatDate(getDate(cfg, fileData)!, cfg.locale)
  }
  const { minutes, text: _timeTaken, words: _words } = readingTime(fileData.text!)
  reading = i18n(cfg.locale).components.contentMeta.readingTime({
    minutes: Math.ceil(minutes),
  })

  const Li = [created, reading]

  return (
    <div
      style={{
        position: "relative",
        display: "flex",
        flexDirection: "row",
        alignItems: "flex-start",
        height: "100%",
        width: "100%",
        backgroundImage: `url("https://${cfg.baseUrl}/static/og-image.jpeg")`,
        backgroundSize: "100% 100%",
      }}
    >
      <div
        style={{
          position: "absolute",
          top: 0,
          left: 0,
          right: 0,
          bottom: 0,
          background: "radial-gradient(circle at center, transparent, rgba(0, 0, 0, 0.4) 70%)",
        }}
      />
      <div
        style={{
          display: "flex",
          height: "100%",
          width: "100%",
          flexDirection: "column",
          justifyContent: "flex-start",
          alignItems: "flex-start",
          gap: "1.5rem",
          paddingTop: "4rem",
          paddingBottom: "4rem",
          marginLeft: "4rem",
        }}
      >
        <img
          src={`"https://${cfg.baseUrl}/static/icon.jpeg"`}
          style={{
            position: "relative",
            backgroundClip: "border-box",
            borderRadius: "6rem",
          }}
          width={80}
        />
        <div
          style={{
            display: "flex",
            flexDirection: "column",
            textAlign: "left",
            fontFamily: fonts[0].name,
          }}
        >
          <h2
            style={{
              color: cfg.theme.colors[colorScheme].light,
              fontSize: "3rem",
              fontWeight: 700,
              marginRight: "4rem",
              fontFamily: fonts[0].name,
            }}
          >
            {title}
          </h2>
          <ul
            style={{
              color: cfg.theme.colors[colorScheme].gray,
              gap: "1rem",
              fontSize: "1.5rem",
              fontFamily: fonts[1].name,
            }}
          >
            {Li.map((item, index) => {
              if (item) {
                return <li key={index}>{item}</li>
              }
            })}
          </ul>
        </div>
        <p
          style={{
            color: cfg.theme.colors[colorScheme].light,
            fontSize: "1.5rem",
            overflow: "hidden",
            marginRight: "8rem",
            textOverflow: "ellipsis",
            display: "-webkit-box",
            WebkitLineClamp: 7,
            WebkitBoxOrient: "vertical",
            lineClamp: 7,
            fontFamily: fonts[1].name,
          }}
        >
          {description}
        </p>
      </div>
    </div>
  )
}
```

## API

- 类别：输出器
- 函数名：`ExternalPlugin.CustomOgImages()`。
- 源码：[`quartz-community/og-image`](https://github.com/quartz-community/og-image)
- 安装：`npx quartz plugin add github:quartz-community/og-image`
