---
date: 2026-09-20
title: 快速开始
---

本指南将带你从零开始搭建 Quartz。如果你已经在首页运行过 [[index#🪴 快速开始|快速开始]]，可以直接跳到当前所处的步骤。

## 前置条件

继续之前，你需要安装以下工具：

- **[Node.js](https://nodejs.org/) v22 或更高版本**（运行 `node -v` 检查）
- **npm v10.9.2 或更高版本**（随 Node 一起提供 —— 运行 `npm -v` 检查）
- **[Git](https://git-scm.com/)**（运行 `git -v` 检查）

> [!warning] 常见问题
>
> - **Linux**：系统软件包（`apt install nodejs`）通常提供的版本要旧得多。请使用 [nvm](https://github.com/nvm-sh/nvm) 或 [NodeSource](https://github.com/nodesource/distributions) 仓库来获取 Node.js v22。
> - **Windows**：安装 Git 时，请确保选中 **"Git from the command line and also from 3rd-party software"**，这样 `git` 才能在终端中使用。如果 `node -v` 或 `git -v` 显示 "command not found"，请重启终端或检查 PATH。
> - **macOS**：Xcode 命令行工具包含 Git（`xcode-select --install`）。至于 Node.js，[nvm](https://github.com/nvm-sh/nvm) 和[官方安装包](https://nodejs.org/)都可以。

## 搭建步骤

按顺序执行以下步骤：

1. **[[installation|安装]]** —— 获取 Quartz（通过 GitHub 模板或克隆）、安装依赖、运行设置向导（`npx quartz create`）、安装插件，并在本地预览站点
2. **[[authoring-content|撰写内容]]** —— 在 `content/` 文件夹中编写并组织你的 Markdown 笔记
3. **[[installation#设置你的 GitHub 仓库|推送到 GitHub]]** —— 创建仓库并用 `npx quartz sync` 推送你的站点
4. **[[hosting|部署]]** —— 在 GitHub Pages、Cloudflare、Netlify 或 Vercel 上免费托管你的站点

## 升级与迁移

- **[[whats-new|Quartz 5 新特性]]** —— 新功能与变更概览
- **[[upgrading|升级 Quartz]]** —— 让你的 Quartz 安装保持最新
- **[[migrating|迁移到 Quartz 5]]** —— 从 Quartz 4 或 Quartz 3 迁移
