---
title: 语法高亮
tags:
  - feature/transformer
---

Quartz 中的语法高亮完全在构建时完成。这意味着 Quartz 只会附带预计算好的 CSS 来高亮正确的词，因此没有执行语法高亮的沉重客户端 bundle。

而且，与一些客户端高亮器不同，它使用完整的 TextMate 解析器语法，而不是正则表达式，从而实现高度准确的代码高亮。

简而言之，它生成的 HTML 看起来与你在 VS Code 等编辑器中的代码完全一样。在底层，它由 [Rehype Pretty Code](https://rehype-pretty-code.netlify.app/) 提供支持，后者使用 [Shiki](https://github.com/shikijs/shiki)。

> [!warning]
> 如果你的笔记中有大量代码片段，语法高亮确实会影响构建速度。

## 格式

一行中 `backticks` 内的文本会像代码一样格式化。

````
```ts
export function trimPathSuffix(fp: string): string {
  fp = clientSideSlug(fp)
  let [cleanPath, anchor] = fp.split("#", 2)
  anchor = anchor === undefined ? "" : "#" + anchor

  return cleanPath + anchor
}
```
````

```ts
export function trimPathSuffix(fp: string): string {
  fp = clientSideSlug(fp)
  let [cleanPath, anchor] = fp.split("#", 2)
  anchor = anchor === undefined ? "" : "#" + anchor

  return cleanPath + anchor
}
```

### 标题

用双引号（`""`）内的文本为代码块添加文件标题：

````
```js title="..."

```
````

```ts title="quartz/path.ts"
export function trimPathSuffix(fp: string): string {
  fp = clientSideSlug(fp)
  let [cleanPath, anchor] = fp.split("#", 2)
  anchor = anchor === undefined ? "" : "#" + anchor

  return cleanPath + anchor
}
```

### 行高亮

在 `{}` 中放置数字范围。

````
```js {1-3,4}

```
````

```ts {2-3,6}
export function trimPathSuffix(fp: string): string {
  fp = clientSideSlug(fp)
  let [cleanPath, anchor] = fp.split("#", 2)
  anchor = anchor === undefined ? "" : "#" + anchor

  return cleanPath + anchor
}
```

### 单词高亮

一串字符，类似字面量正则表达式。

````
```js /useState/
const [age, setAge] = useState(50);
const [name, setName] = useState('Taylor');
```
````

```js /useState/
const [age, setAge] = useState(50)
const [name, setName] = useState("Taylor")
```

### 行内高亮

在行内代码末尾附加 {:lang}，使其像普通代码块一样高亮。

```
This is an array `[1, 2, 3]{:js}` of numbers 1 through 3.
```

这是一个由数字 1 到 3 组成的数组 `[1, 2, 3]{:js}`。

### 行号

语法高亮已自动配置行号。如果你想让行号从特定数字开始，请使用 `showLineNumbers{number}`：

````
```js showLineNumbers{number}

```
````

```ts showLineNumbers{20}
export function trimPathSuffix(fp: string): string {
  fp = clientSideSlug(fp)
  let [cleanPath, anchor] = fp.split("#", 2)
  anchor = anchor === undefined ? "" : "#" + anchor

  return cleanPath + anchor
}
```

### 转义代码块

你可以通过在代码块外再包一层反引号围栏来在代码块内格式化代码块，该围栏比前一层多一个反引号。

`````
````
```js /useState/
const [age, setAge] = useState(50);
const [name, setName] = useState('Taylor');
```
````
`````

## 自定义

语法高亮是 [[SyntaxHighlighting]] 插件的功能。有关自定义选项，请参阅该插件页面。
