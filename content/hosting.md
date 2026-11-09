---
date: 2026-09-20
title: 托管
---

Quartz 会将你的 Markdown 文件和其他资源打包成一组 HTML、JS 和 CSS 文件（也就是一个网站！）。

不过，如果你想将站点发布给全世界，就需要一个在线托管它的方式。本指南将详细介绍如何用常见的托管服务商进行部署，但任何允许部署静态 HTML 的服务同样可行。

> [!warning]
> 本指南的其余部分假设你已经为 Quartz 创建了自己的 GitHub 仓库。如果还没有，请按安装指南中 [[installation#设置你的 GitHub 仓库|GitHub 仓库设置]] 一节操作。

> [!hint]
> Quartz 的一些功能（如 [[RSS Feed]] 和站点地图生成）需要你在 [[configuration]] 中正确配置 `baseUrl` 才能正常工作。部署前请务必设置好！

> [!tip] 保持插件同步
> 下面所有托管示例都使用 `npx quartz plugin install` 从 lockfile 安装插件。如果贡献者可能向 `quartz.config.yaml` 添加插件却没有更新 lockfile，请在构建命令中 `install` 之后加上 `npx quartz plugin install --from-config`，以安装任何缺失的插件。详见 [[cli/plugin#install|plugin install]]。

## Cloudflare Pages

1. 登录 [Cloudflare 仪表盘](https://dash.cloudflare.com/) 并选择你的账户。
2. 在 Account Home 中，依次选择 **Compute (Workers)** > **Workers & Pages** > **Create application** > **Pages** > **Connect to Git**。
3. 选择你新建的 GitHub 仓库，并在 **Set up builds and deployments** 区域提供以下信息：

| 配置选项               | 值                                              |
| ---------------------- | ----------------------------------------------- |
| 生产分支               | `v5`                                            |
| 框架预设               | `None`                                          |
| 构建命令               | `npx quartz plugin install && npx quartz build` |
| 构建输出目录           | `public`                                        |

点击「Save and deploy」，Cloudflare 大约一分钟内就会部署好你的站点。之后，每次你把 Quartz 的更改同步到 GitHub，站点都会随之更新。

要添加自定义域名，请参阅 [Cloudflare 的文档](https://developers.cloudflare.com/pages/platform/custom-domains/)。

> [!warning]
> Cloudflare Pages 默认执行浅克隆，因此如果你依赖 `git` 获取时间戳，建议在构建命令开头加上 `git fetch --unshallow &&`（例如 `git fetch --unshallow && npx quartz plugin install && npx quartz build`）。

> [!note]
> 关于更详细的 CI/CD 配置（包括缓存和插件管理），请参阅 [[migrating#更新你的 CI/CD|迁移指南]]。

## GitHub Pages

在你的本地 Quartz 中，新建文件 `quartz/.github/workflows/deploy.yml`。

```yaml title="quartz/.github/workflows/deploy.yml"
name: Deploy Quartz site to GitHub Pages

on:
  push:
    branches:
      - v5

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 0 # 拉取完整历史以获取 git 信息
      - uses: actions/setup-node@v6
        with:
          node-version: 24
      - name: Cache dependencies
        uses: actions/cache@v5
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-
      - name: Cache Quartz plugins
        uses: actions/cache@v5
        with:
          path: .quartz/plugins
          key: ${{ runner.os }}-plugins-${{ hashFiles('quartz.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-plugins-
      - name: Install Dependencies
        run: npm ci
      - name: Install Quartz plugins
        run: npx quartz plugin install
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

然后：

1. 前往你 fork 的仓库的「Settings」标签页，在侧边栏中点击「Pages」。在「Source」下选择「GitHub Actions」。
2. 运行 `npx quartz sync` 提交这些更改。这会将站点部署到 `<github-username>.github.io/<repository-name>`。

> [!hint]
> 如果遇到因环境保护规则而不允许部署到 `github-pages` 的错误，请确保删除所有已存在的 GitHub pages 环境。
>
> 具体做法是：前往你 GitHub fork 的 Settings 页面，进入 Environments 标签页并点击垃圾桶图标。下次同步 Quartz 时，GitHub Action 会正确地为你重新创建该环境。

> [!info]
> Quartz 生成的文件格式是 `file.html` 而不是 `file/index.html`，这意味着 _非文件夹路径_ 的结尾斜杠会被去掉。由于 GitHub pages 不做这种重定向，使用结尾斜杠指向你站点的现有链接可能会失效。如果保持现有链接可用对你很重要（例如你正从 Quartz 3 迁移），可以考虑使用 [[#Cloudflare Pages]]。

### 自定义域名

下面介绍如何为你的 GitHub pages 部署添加自定义域名。

1. 前往你 fork 的仓库的「Settings」标签页。
2. 在侧边栏的「Code and automation」区域中点击「Pages」。
3. 在「Custom Domain」下输入你的自定义域名，然后点击「Save」。
4. 下一步取决于你使用的是顶级域名（`example.com`）还是子域名（`subdomain.example.com`）。
   - 如果使用顶级域名，请前往你的 DNS 服务商，创建一条 `A` 记录，将顶级域名指向 GitHub 的域名服务器，其 IP 地址如下：
     - `185.199.108.153`
     - `185.199.109.153`
     - `185.199.110.153`
     - `185.199.111.153`
   - 如果使用子域名，请前往你的 DNS 服务商，创建一条 `CNAME` 记录，将子域名指向站点的默认域名。例如，如果你想为用户站点使用子域名 `quartz.example.com`，请创建一条 `CNAME` 记录，将 `quartz.example.com` 指向 `<github-username>.github.io`。

![[dns records.png]]_上图展示了一个 Google Domains 的配置截图，其中同时配置了 `jzhao.xyz`

（顶级域名）和 `quartz.jzhao.xyz`

（子域名）。_

关于如何为 GitHub Pages 设置你自己的自定义域名，更多细节请参阅 [GitHub 文档](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain)。

> [!question] 为什么我的更改没有显示出来？
> 你的更改没有显示出来可能有多种原因，但最可能的原因是你忘记把更改推送到 GitHub 了。
>
> 请确保把更改保存到 Git，并运行 `npx quartz sync` 同步到 GitHub。这也会拉取你可能在其他设备上做出的更新，使它们在本地可用。

## Vercel

### 修复 URL

在部署到 Vercel 之前，项目目录根目录下需要一个 `vercel.json` 文件。它需要包含以下配置，以便 URL 不必带 `.html` 扩展名：

```json title="vercel.json"
{
  "cleanUrls": true
}
```

### 部署到 Vercel

1. 登录 [Vercel 仪表盘](https://vercel.com/dashboard)，点击「Add New...」> Project
2. 导入包含你 Quartz 项目的 Git 仓库。
3. 为项目命名（只能使用小写字母和连字符）
4. 确认以下配置项已设置：

| 配置选项                                  | 值                                              |
| ----------------------------------------- | ----------------------------------------------- |
| 框架预设                                  | `Other`                                         |
| 根目录                                    | `./`                                            |
| 构建与输出设置 > 构建命令                 | `npx quartz plugin install && npx quartz build` |

5. 点击 Deploy。上线后，你会获得 2 个 `*.vercel.app` URL 用于查看页面。

### 自定义域名

> [!note]
> 如果该域名上已经托管了其他内容，那么在替换原有内容之前这些步骤无法生效。作为变通方案，你可以使用 Next.js rewrites，或者使用下一节来创建子域名。

1. 如有必要，更新 `quartz.config.yaml` 中的 `baseUrl`。
2. 前往 Vercel 中的 [Domains - Dashboard](https://vercel.com/dashboard/domains) 页面。
3. 将域名连接到 Vercel
4. 点击「Add」将自定义域名连接到 Vercel。
5. 选择你的 Quartz 仓库并点击 Continue。
6. 输入你想连接到的域名。
7. 按说明更新 DNS 记录，直到看到「Valid Configuration」

### 使用子域名

`docs.example.com` 就是子域名的一个例子。子域名是将多个部署连接到同一个域名的简单方式。

1. 如有必要，更新 `quartz.config.yaml` 中的 `baseUrl`。
2. 确保你的域名已添加到 Vercel 的 [Domains - Dashboard](https://vercel.com/dashboard/domains) 页面。
3. 前往 [Vercel 仪表盘](https://vercel.com/dashboard) 并选择你的 Quartz 项目。
4. 进入 Settings 标签页，然后点击侧边栏中的 Domains
5. 在字段中输入你的子域名并点击 Add

## Netlify

1. 登录 [Netlify 仪表盘](https://app.netlify.com/)，点击「Add new site」。
2. 选择你的 Git 服务商以及包含 Quartz 项目的仓库。
3. 在「Build command」下输入 `npx quartz plugin install && npx quartz build`。
4. 在「Publish directory」下输入 `public`。
5. 点击 Deploy。上线后，你会获得一个 `*.netlify.app` URL 用于查看页面。
6. 要添加自定义域名，请在左侧边栏查看「Domain management」，与 Vercel 类似。

## GitLab Pages

在你的本地 Quartz 中，新建文件 `.gitlab-ci.yml`。

```yaml title=".gitlab-ci.yml"
stages:
  - build
  - deploy

image: node:24
cache:
  - key: npm-$CI_COMMIT_REF_SLUG
    paths:
      - .npm/
  - key: plugins-$CI_COMMIT_REF_SLUG
    paths:
      - .quartz/plugins/

build:
  stage: build
  rules:
    - if: '$CI_COMMIT_REF_NAME == "v5"'
  before_script:
    - hash -r
    - npm ci --cache .npm --prefer-offline
  script:
    - npx quartz plugin install
    - npx quartz build
  artifacts:
    paths:
      - public

pages:
  stage: deploy
  rules:
    - if: '$CI_COMMIT_REF_NAME == "v5"'
  script:
    - echo "Deploying to GitLab Pages..."
  artifacts:
    paths:
      - public
```

提交 `.gitlab-ci.yaml` 后，GitLab 会构建网站并将其部署为 GitLab Page。你可以在侧边栏的 `Deploy > Pages` 下找到 URL。

默认情况下，该页面是私有的，只有登录到具有该仓库访问权限的 GitLab 账户才可见，但你可以在设置中的 `Deploy` -> `Pages` 下将其开放。

## 自托管

将 `public` 目录复制到你的 Web 服务器，并配置它以提供这些文件。你可以使用任何 Web 服务器托管站点。由于 Quartz 生成的链接不含 `.html` 扩展名，你需要让 Web 服务器知道如何处理这种情况。

### 使用 Nginx

以下是使用 Nginx 实现这一点的示例：

```nginx title="nginx.conf"
server {
    listen 80;
    server_name example.com;
    root /path/to/quartz/public;
    index index.html;
    error_page 404 /404.html;

    location / {
        try_files $uri $uri.html $uri/ =404;
    }
}
```

### 使用 Apache

以下是使用 Apache 实现这一点的示例：

```apache title=".htaccess"
RewriteEngine On

ErrorDocument 404 /404.html

# 去除 .html 扩展名的重写规则（含目录检查）
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_URI}.html -f
RewriteRule ^(.*)$ $1.html [L]

# 显式处理目录请求
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^(.*)/$ $1/index.html [L]
```

别忘了启用 brotli / gzip 压缩。

### 使用 Caddy

以下是使用 Caddy 实现这一点的示例：

```caddy title="Caddyfile"
example.com {
    root * /path/to/quartz/public
    try_files {path} {path}.html {path}/ =404
    file_server
    encode gzip

    handle_errors {
        rewrite * /{err.status_code}.html
        file_server
    }
}
```

## 缓存

Quartz 输出的 CSS 和 JS 文件名中包含内容哈希（例如 `index-a3f2c1b.css`、`component-7d4e2f.css`）。由于内容一旦变化文件名就会改变，这些文件可以无限期缓存。HTML 文件不应长期缓存，因为它们引用了带哈希的文件名，需要保持最新。

### Cloudflare Pages / Vercel / Netlify

这些平台会自动处理缓存。无需任何配置 —— 带哈希的资源开箱即会附带合适的缓存头。

### Nginx

```nginx title="nginx.conf"
# 为带哈希的资源设置不可变缓存
location ~* \.(css|js)$ {
    if ($uri ~* "-[0-9a-f]{8}\.") {
        add_header Cache-Control "public, max-age=31536000, immutable";
    }
}
```

### Caddy

```caddy title="Caddyfile"
@hashed path_regexp hashed -[0-9a-f]{8}\.(css|js)$
header @hashed Cache-Control "public, max-age=31536000, immutable"
```

### Apache

```apache title=".htaccess"
# 为带内容哈希的资源设置不可变缓存
<FilesMatch "-[0-9a-f]{8}\.(css|js)$">
    Header set Cache-Control "public, max-age=31536000, immutable"
</FilesMatch>
```
