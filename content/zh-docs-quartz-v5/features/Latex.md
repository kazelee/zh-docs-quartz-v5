---
title: LaTeX
tags:
  - feature/transformer
---

Quartz 默认使用 [Katex](https://katex.org/) 在构建时排版行内与块级数学表达式。

## 语法

### 块级数学

块级数学可以通过用 `$$` 界定数学表达式来渲染。

```
$$
f(x) = \int_{-\infty}^\infty
    f\hat(\xi),e^{2 \pi i \xi x}
    \,d\xi
$$
```

$$
f(x) = \int_{-\infty}^\infty
    f\hat(\xi),e^{2 \pi i \xi x}
    \,d\xi
$$

$$
\begin{aligned}
a &= b + c \\ &= e + f \\
\end{aligned}
$$

$$
\begin{bmatrix}
1 & 2 & 3 \\
a & b & c
\end{bmatrix}
$$

$$
\begin{array}{rll}
E \psi &= H\psi & \text{Expanding the Hamiltonian Operator} \\
&= -\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x^2} \psi + \frac{1}{2}m\omega x^2 \psi & \text{Using the ansatz $\psi(x) = e^{-kx^2}f(x)$, hoping to cancel the $x^2$ term} \\
&= -\frac{\hbar^2}{2m} [4k^2x^2f(x)+2(-2kx)f'(x) + f''(x)]e^{-kx^2} + \frac{1}{2}m\omega x^2 f(x)e^{-kx^2} &\text{Removing the $e^{-kx^2}$ term from both sides} \\
& \Downarrow \\
Ef(x) &= -\frac{\hbar^2}{2m} [4k^2x^2f(x)-4kxf'(x) + f''(x)] + \frac{1}{2}m\omega x^2 f(x) & \text{Choosing $k=\frac{im}{2}\sqrt{\frac{\omega}{\hbar}}$ to cancel the $x^2$ term, via $-\frac{\hbar^2}{2m}4k^2=\frac{1}{2}m \omega$} \\
&= -\frac{\hbar^2}{2m} [-4kxf'(x) + f''(x)] \\
\end{array}
$$

> [!warn]
> 由于[底层解析库](https://github.com/remarkjs/remark-math)的限制，Quartz 中的块级数学要求 `$$` 定界符像上面那样位于单独的行上。

### 行内数学

类似地，行内数学可以通过用单个 `$` 界定数学表达式来渲染。例如，`$e^{i\pi} = -1$` 会生成 $e^{i\pi} = -1$

### 转义符号

有时一个段落中可能同时出现多个 `$`，这可能会意外触发 MathJax/Katex。

为避免这种情况，你可以用 `\$` 来转义美元符号。

例如：

- 错误：`I have $1 and you have $2` 会生成 I have $1 and you have $2
- 正确：`I have \$1 and you have \$2` 会生成 I have \$1 and you have \$2

### 使用 mhchem

如果你使用的是社区 Latex 插件，可以通过 fork 插件仓库，并在 `src/index.ts` 顶部（所有其他 import 之前）添加以下 import 来支持 `mhchem`：

```ts title="src/index.ts"
import "katex/contrib/mhchem"
```

## 自定义

Latex 解析是 [[plugins/Latex|Latex]] 插件的功能。有关自定义选项，请参阅插件页面。
