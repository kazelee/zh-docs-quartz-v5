---
date: 2026-09-20
title: "安装"
aliases:
  - "setting up your GitHub repository"
---

本页将带你完成完整的 Quartz 设置流程：从获取源代码，到在本地预览站点，再到把它推送到 GitHub。

## 1. 获取 Quartz

有两种开始方式，选择你更喜欢的一种：

### 方式 A：使用 GitHub 模板（推荐）

> [!tip] 为什么选这种方式？
> 使用模板可以一键创建你自己的仓库 —— 之后无需重新配置 Git 远程地址。

1. 打开 [Quartz 仓库](https://github.com/jackyzha0/quartz)，点击 **Use this template** → **Create a new repository**
2. 为仓库起一个名字（例如 `quartz`、`notes`、`garden`），选择公开或私有，然后点击 **Create repository**
3. 克隆 **你的新仓库** 并进入该文件夹：

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
```

### 方式 B：直接克隆

如果你不使用 GitHub，或更倾向于手动设置：

```bash
git clone https://github.com/jackyzha0/quartz.git
cd quartz
```

> [!note]
> 使用这种方式，稍后准备发布时，你需要把 `origin` 远程 [[#连接你的本地克隆|指向你自己的仓库]]。

## 2. 安装依赖

> [!important]
> Quartz 需要 **Node.js 22** 或更高版本。用 `node -v` 检查你的版本，如有需要请在 [nodejs.org](https://nodejs.org/) 升级。

```bash
npm i
```

> [!note]
> 之后在你自己的仓库上再次克隆时（例如在一台新机器上），请改用 `npm ci`，以便从锁文件进行更快、可复现的安装。

## 3. 初始化站点

运行交互式设置向导：

```bash
npx quartz create
```

它会提示你输入以下内容：

- 一个 **模板**（`default`、`obsidian`、`ttrpg`、`blog`）—— 选择符合你使用场景的那一个。各类模板的详细信息见 [[create#模板]]。
- 一个 **内容策略** —— 选择如何填充 `content/` 文件夹：
  - **new**：从空文件夹开始
  - **copy**：从已有文件夹复制文件（例如你的 Obsidian 库）
  - **symlink**：链接到已有文件夹，使更改自动同步
- 一个 **baseUrl** —— 你的站点将要部署到的 URL（例如 `mysite.github.io/quartz`）。不要包含 `https://`。
- 一个 **链接解析** 策略 —— 如何解析内部链接（`shortest`、`absolute` 或 `relative`）。Obsidian 和 TTRPG 模板会跳过此项。

非交互式用法及更多细节，请参见 [[create|`quartz create` CLI 参考]]。

## 4. 安装插件

你选择的模板引用了需要安装的社区插件：

```bash
npx quartz plugin install --from-config
```

这会把 `quartz.config.yaml` 中列出的所有插件下载并构建到 `.quartz/plugins/`。

> [!tip]
> 如果某些插件构建失败，可以尝试把它们刷新到最新版本：
>
> ```bash
> npx quartz plugin install --latest
> ```
>
> 更多细节见 [[troubleshooting#全新克隆后插件构建失败]]。

## 5. 预览站点

```bash
npx quartz build --serve
```

你的站点现在运行在 `http://localhost:8080`。开发服务器会监视文件更改并自动重载。

此时你可以在 `content/` 文件夹中 [[authoring-content|开始撰写内容]]。准备好发布后，继续阅读下文，把站点推送到 GitHub 并 [[hosting|部署它]]。

---

## 设置你的 GitHub 仓库

> [!note]
> 如果你在步骤 1 中使用了 **方式 A**（GitHub 模板），仓库已经存在，`origin` 也已设置好。你可以直接跳到 [[#推送你的站点]]。

要发布站点，你需要自己的 GitHub 仓库。本节面向 **方式 B**（直接克隆）的用户。

### 创建仓库

在 [GitHub.com](https://github.com/new) 上创建一个新仓库。**不要** 用 README、许可证或 `.gitignore` 初始化它 —— Quartz 已经包含这些文件，重复添加会在你首次推送时造成合并冲突。

![[github-init-repo-options.png]]

从 Quick Setup 页面复制仓库 URL：

![[github-quick-setup.png]]

### 连接你的本地克隆

把你的本地 Quartz 指向新仓库：

```bash
# 检查当前的远程地址
git remote -v

# 将 origin 指向你的仓库
git remote set-url origin REMOTE-URL
```

> [!tip]
> 你无需手动添加 `upstream` 远程 —— `npx quartz create` 已经为你配置好了。`npx quartz upgrade` 会使用 upstream 远程来拉取未来的 Quartz 更新。

### 推送你的站点

```bash
npx quartz sync --no-pull
```

这会提交你的内容，并把所有内容推送到你的仓库。之后的更新只需运行：

```bash
npx quartz sync
```

> [!hint] 标志与选项
> 完整的帮助选项可以运行 `npx quartz sync --help` 查看。
>
> 其中大多数都有合理的默认值，但如果你有自定义设置，也可以覆盖它们：
>
> - `-d` 或 `--directory`：内容文件夹。通常就是 `content`
> - `-v` 或 `--verbose`：打印额外的日志信息
> - `--commit` 或 `--no-commit`：是否为你的更改创建 `git` 提交
> - `--push` 或 `--no-push`：是否把更新推送到你 Fork 的 Quartz GitHub 仓库
> - `--pull` 或 `--no-pull`：是否在推送前尝试拉取你 GitHub fork 中的更新（即来自其他设备的更新）

## 后续步骤

- **[[authoring-content|撰写内容]]** —— 编写并组织你的笔记
- **[[hosting|托管]]** —— 将站点部署到 GitHub Pages、Cloudflare、Netlify 或 Vercel
- **[[configuration|配置]]** —— 自定义站点的外观与行为
