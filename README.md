# Quartz v5 教程文档中文翻译

> 本项目完全使用 AI 翻译，没有经过人工校对，不代表官方，仅为个人自用，内容仅供参考。

## 项目说明

1. 使用 DSH 批量翻译，将 `doc/` 下的所有 markdown 文件和 canvas 文件内容翻译，并复制到 `content/` 目录下。
2. 保留原本的文件结构和文件名称，部分文件内容（如标签、链接等）保留英文原文。
3. 修复了部分中文斜体样式没有正常渲染的问题（`_中文_` 周围必须有空格或者符号，否则不会视作斜体）
4. 文档目前的版本基于 [Quartz v5](https://github.com/jackyzha0/quartz) 仓库 [Commit 97a2d05](https://github.com/jackyzha0/quartz/commit/97a2d05f80c4c50534959b1d0d41cc4b3895625e) 版本翻译，大概率不会更新，内容仅供参考。

<!--
1. 简单使用 DSH 翻译 Quartz v5 文档的内容。

```
这是 quartz 仓库的模板，其中 @docs/ 下记录了 quartz v5 的相关教程，现在，我在 @content/ 目录下新建了一个文件夹 @content/zh-docs-quartz-v5/ ，需要你将 @docs/ 下的文件翻译成中文并创建到这个文件夹下，不要改变原本 @docs/ 下的文件，只翻译 markdown 文件，对于其他文件，直接调用系统命令复制，不要做其他操作。对于 markdown 文件，不要改变文件名，只翻译文件内部的内容。你需要保留原本的 markdown 的相关格式标记，此外，对于 markdown 文件的元数据，只翻译 标题 和 描述 的部分为中文，其他的，如 标签 和 链接 等其他元数据，保留原本的英文。
```

2. 手动调整了 `Base.base` 和 `zh-docs-quartz-v5/plugins/index.md` 文件中内嵌 `base` 代码中的文件路径，使页面正常显示表格内容。
3. 保留了文件的英文名，保留了标签等元素的英文原词，保留 `Canvas.canvas` 文件的英文原文，没有进一步翻译。

## 项目优化补充

实测发现，还是存在一些链接对应不上的问题。比如，与本 `README.md` 内容相同，用于说明翻译情况的 `content/index.md` 被 `content/zh-docs-quartz-v5/getting-started/index.md` 的 `[[index#🪴 Get Started|快速开始]]` 引用。为了减少不必要的折腾，删除 `content/zh-docs-quartz-v5/` 文件夹，改成和 [Quartz 官网](https://quartz.jzhao.xyz/) 一样的结构。恢复部分 `base` 代码。

```
content   [OLD VERSION]
 │  index.md            (updated by `index.md` from `zh-docs-quartz-v5`)
 │  
 └─zh-docs-quartz-v5    (deleted)
    ├─cli               (moved from `zh-docs-quartz-v5` to `content`)
    ├─features          (moved from `zh-docs-quartz-v5` to `content`)
    ├─getting-started   (moved from `zh-docs-quartz-v5` to `content`)
    ├─images            (moved from `zh-docs-quartz-v5` to `content`)
    ├─plugins           (moved from `zh-docs-quartz-v5` to `content`)
    └─tags              (moved from `zh-docs-quartz-v5` to `content`)
```
-->
