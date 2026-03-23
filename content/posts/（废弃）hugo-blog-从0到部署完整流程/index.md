---
title: "(废弃)hugo-blog-从0到部署完整流程" # 标题
date: 2026-03-23T20:00:21+08:00 # 发布时间
lastmod: 2026-03-23T20:00:21+08:00 # 修改时间，为了seo搜索，告诉google你修改了文章 
author: "uaena" #作者
categories: ["折腾"] # ["分类","分类2"]
tags: ["Hugo","GitHub","博客"] # ["标签","标签2"]
description: "摘要"
draft: false # 草稿状态关闭=flase，开启为true
ShowToc: true # true=开启目录
TocOpen: false # false=目录展开
# 分类	标签
# 代码	C++, Python, 二叉树, LeetCode
# 折腾	Hugo, Git, 代理, Gemini
# 投资	A股, 美股, 加密货币, ETF
# 英语	口语, 词汇, 阅读
# 生活	CF, 洛克王国, 时间管理, 复盘
---

本文已废弃，请跳转最新[Hugo 博客从0-1构建]({{< ref "posts/Hugo博客从0到1构建" >}})
# Hugo 博客从 0 到部署上线（最终版完整流程）

这篇只写最终可执行方案：

- GitHub 先建仓库
- Windows 本地初始化
- 安装 Hugo
- 接入 Monochrome
- 本地预览
- GitHub Actions 自动部署到 GitHub Pages

已知前提：

- 已安装 **Git**
- 已安装 **VS Code**
- 仓库名直接使用 **`uaenawook.github.io`**
- 主题直接使用 **Monochrome**

---

# 0. 命令执行目录

后面凡是执行 `git`、`hugo`、`mkdir content\...`、`hugo new ...`、`hugo server -D` 这类命令时，默认都要求你先进入博客项目根目录再执行。

博客项目根目录就是这个文件夹：

```text
uaenawook.github.io
```

也就是能看到这些文件和目录的位置：

```text
hugo.toml
content/
themes/
static/
archetypes/
.github/
```

如果当前文件夹里看不到 `hugo.toml`，那大概率就不是 Hugo 项目根目录。

只有刚开始创建本地文件夹时，下面这两句是在项目根目录之外执行：

```bash
mkdir uaenawook.github.io
cd uaenawook.github.io
```

从执行完 `cd uaenawook.github.io` 开始，后面大多数命令都默认在这个目录里执行。


# 1. 在 GitHub 创建仓库

> 执行位置：**GitHub 网页后台**

步骤：

1. 打开 GitHub
2. 右上角点击 `+`
3. 点击 `New repository`
4. Repository name 填：`uaenawook.github.io`
5. 建议选择 `Public`
6. **不要勾选**：
   - Add a README file
   - Add .gitignore
   - Choose a license
7. 点击 `Create repository`

说明：

- 你要部署的是 **GitHub Pages 用户站点**
- 用户站点仓库名必须是：`用户名.github.io`
- 所以上线地址就是：

```text
https://uaenawook.github.io
```

---

# 2. 在本地创建项目文件夹

> 执行位置：**Git Bash**

先在资源管理器里打开你准备存放博客的上级文件夹，然后在空白处右键，点击 **Git Bash Here**。  
如果你要打开 PowerShell 或 CMD：点击资源管理器地址栏，输入 `powershell` 或 `cmd` 后回车。

执行：

```bash
mkdir uaenawook.github.io
cd uaenawook.github.io
```

作用：

- 创建本地项目根目录
- 后续所有 Hugo 和 Git 操作，都围绕这个目录进行

---

# 3. 初始化本地 Git 仓库

> 执行位置：**Git Bash**

在项目根目录执行：

```bash
git init
git branch -M main
git remote add origin https://github.com/uaenawook/uaenawook.github.io.git
```

作用：

- `git init`：初始化本地仓库
- `git branch -M main`：把默认分支改成 `main`
- `git remote add origin ...`：绑定远程 GitHub 仓库

到这里，本地目录已经和 GitHub 上的空仓库建立连接。

---

# 4. 安装 Hugo

## 方案 A：winget 安装（推荐）

> 执行位置：**PowerShell**

```powershell
winget install Hugo.Hugo.Extended
```

---

## 方案 B：Chocolatey 安装

> 执行位置：**PowerShell**

```powershell
choco install hugo-extended
```

---

## 方案 C：Scoop 安装

> 执行位置：**PowerShell**

```powershell
scoop install hugo-extended
```

---

## 为什么安装 extended 版本

因为很多 Hugo 主题依赖 SCSS / SASS。  
为了避免主题构建报错，直接安装 **extended** 版本最稳。

---

# 5. 验证 Hugo 是否安装成功

> 执行位置：**Git Bash**

执行：

```bash
hugo version
```

如果能看到版本号，说明 Hugo 安装成功。  
如果输出里带有 `extended`，说明你装的是扩展版。

---

# 6. 在当前目录初始化 Hugo 站点

> 执行位置：**Git Bash**

确认你现在位于：

```text
uaenawook.github.io
```

然后执行：

```bash
hugo new site . --force
```

作用：

- 在当前目录初始化 Hugo 项目
- `--force` 表示允许在当前非空目录中创建 Hugo 站点

执行完后，项目里会出现 Hugo 基础结构，例如：

```text
archetypes/
content/
layouts/
static/
themes/
hugo.toml
```

---

# 7. 安装 Monochrome 主题

> 执行位置：**Git Bash**

在项目根目录执行：

```bash
git submodule add https://github.com/kaiiiz/hugo-theme-monochrome.git themes/hugo-theme-monochrome
```

作用：

- 把 Monochrome 主题作为 **Git submodule** 挂载到 `themes/` 目录
- 以后可以单独更新主题
- 部署时也方便递归拉取主题

---

# 8. 修改 `hugo.toml`

> 执行位置：**VS Code 编辑文件**
>
> 不是终端命令，是直接编辑文件。

打开根目录下的 `hugo.toml`，先写成这份最小可用版本：

```toml
baseURL = "https://uaenawook.github.io/"
languageCode = "zh-cn"
title = "UaenaWook Blog"
theme = "hugo-theme-monochrome"

[pagination]
pagerSize = 10

[taxonomies]
category = "categories"
tag = "tags"

[outputs]
home = ["HTML", "JSON"]

[params]
description = "个人博客"
navbar_title = "UaenaWook Blog"
footer = "Copyright © 2026 uaenawook"

[[menu.navbar]]
identifier = "posts"
name = "文章"
url = "/posts/"
weight = 1

[[menu.navbar]]
identifier = "solutions"
name = "题解"
url = "/solutions/"
weight = 2

[[menu.navbar]]
identifier = "moments"
name = "说说"
url = "/moments/"
weight = 3

[[menu.navbar]]
identifier = "about"
name = "关于"
url = "/about/"
weight = 4
```

---

## `hugo.toml` 每个字段是什么意思

### `baseURL`
站点根地址。

你的仓库是用户站点，所以必须写：

```toml
baseURL = "https://uaenawook.github.io/"
```

如果写错，常见问题是：

- 图片路径错
- CSS 加载失败
- 页面跳转路径错
- 部分资源 404

---

### `languageCode`
站点语言代码。  
中文通常写：

```toml
languageCode = "zh-cn"
```

---

### `title`
网站标题。  
常用于浏览器标签页标题、主题中的站点名称等。

---

### `theme`
当前主题目录名。  
必须和 `themes/` 下面的主题文件夹名一致：

```toml
theme = "hugo-theme-monochrome"
```

---

### `[pagination]`
分页设置。

```toml
[pagination]
pagerSize = 10
```

意思是每页显示 10 篇内容。

---

### `[taxonomies]`
分类与标签设置。

```toml
[taxonomies]
category = "categories"
tag = "tags"
```

意思是：

- 分类使用 `categories`
- 标签使用 `tags`

---

### `[outputs]`
控制 Hugo 输出哪些格式。

```toml
[outputs]
home = ["HTML", "JSON"]
```

含义：

- 首页输出 HTML
- 同时输出 JSON

为什么保留 JSON：

- 许多 Hugo 主题的搜索功能依赖 JSON 索引
- 不开 JSON，搜索可能无法正常工作

---

### `[params]`
主题参数区。

```toml
[params]
description = "个人博客"
navbar_title = "UaenaWook Blog"
footer = "Copyright © 2026 uaenawook"
```

通常用来控制：

- 网站描述
- 导航栏标题
- 页脚文字
- 头像
- 搜索开关
- TOC 开关
- 数学公式开关

不同主题的 `params` 字段不一样。  
这里先用最基础的。

---

### `[[menu.navbar]]`
导航栏菜单项。  
一个菜单项写一组。

例如：

```toml
[[menu.navbar]]
identifier = "posts"
name = "文章"
url = "/posts/"
weight = 1
```

含义：

- `identifier`：内部标识名
- `name`：菜单显示名称
- `url`：点击后跳转地址
- `weight`：排序，数字越小越靠前

---

# 9. 创建内容目录

> 执行位置：**Git Bash**

在项目根目录执行：

```bash
mkdir -p content/posts content/solutions content/moments
```

作用：

- `posts`：正式文章
- `solutions`：题解
- `moments`：说说

---

# 10. 创建首页、关于页、说说栏目页

> 执行位置：**VS Code 编辑文件**  
> 不是终端命令

手动创建这些文件：

```text
content/_index.md
content/about.md
content/moments/_index.md
```

---

## `content/_index.md`

```md
---
title: ""
type: "balloon"
balloon_img_src: "img/avatar.jpg"
balloon_circle: true
---

# UaenaWook

静能破局，慢可生根
```

作用：

- 控制首页标题
- 控制首页展示方式
- 控制首页头像展示

---

## `content/about.md`

```md
---
title: "关于"
---

这里写你的个人介绍。
```

作用：

- 关于页内容

---

## `content/moments/_index.md`

```md
---
title: "说说"
type: "balloon"
---

这里记录日常碎片。
```

作用：

- “说说”栏目页内容
- 说说列表页说明文字

---

# 11. 放静态资源

> 执行位置：**Git Bash** 创建目录  
> 文件复制操作在 **资源管理器** 或 **VS Code** 中完成

先创建目录：

```bash
mkdir -p static/img
```

然后把资源放进去：

- `static/avatar.jpg`
- `static/favicon.ico`
- 其他全站通用图片放 `static/img/`

说明：

- `static` 里的文件会被 Hugo 原样复制到最终站点
- 适合放头像、图标、全站通用图片、下载附件等

---

# 12. 配置新文章模板

> 执行位置：**VS Code 编辑文件**

打开：

```text
archetypes/default.md
```

改成：

```md
---
title: "{{ replace .File.ContentBaseName `-` ` ` | title }}"
date: {{ .Date }}
lastmod: {{ .Date }}
author: "uaenawook"
categories: []
tags: []
description: ""
draft: true
ShowToc: true
TocOpen: false
---
```

作用：

- 以后新建文章时，自动带上统一 Front Matter
- 不需要每篇文章手抄一遍头信息

---

# 13. 本地启动预览

> 执行位置：**Git Bash**

确保当前目录是项目根目录，然后执行：

```bash
hugo server -D
```

浏览器打开：

```text
http://localhost:1313
```

作用：

- 本地预览博客效果
- `-D` 表示连 `draft: true` 的草稿文章也一起显示

---

# 14. 写第一篇文章

## 正确做法：直接创建到目标目录

> 执行位置：**Git Bash**

如果是正式文章：

```bash
hugo new posts/my-first-post/index.md
```

如果是题解：

```bash
hugo new solutions/binary-tree/index.md
```

如果是说说：

```bash
hugo new moments/2026-03-23.md
```

说明：

- 这会自动套用 `archetypes/default.md`
- 不要先 `hugo new index.md` 再手动拖文件
- 直接一步创建到目标位置最稳

---

## 推荐文章结构

```text
content/posts/my-first-post/
├── index.md
└── cover.png
```

如果图片和文章在同目录，正文直接写：

```md
![封面](cover.png)
```

---

# 15. 创建 GitHub Actions 工作流

## 第一步：创建目录

> 执行位置：**Git Bash**

```powershell
mkdir .github
mkdir .github\workflows
```

---

## 第二步：创建工作流文件

> 执行位置：**VS Code 编辑文件**

创建文件：

```text
.github/workflows/hugo.yml
```

内容写成：

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.152.2
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: ${{ env.HUGO_VERSION }}
          extended: true

      - name: Build with Hugo
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    permissions:
      pages: write
      id-token: write
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

说明：

- `submodules: recursive`：递归拉取主题子模块
- `extended: true`：确保 Hugo 用扩展版
- `path: ./public`：部署 Hugo 生成的静态网页目录
- `pages: write` 和 `id-token: write`：Pages 部署需要的权限

---

# 16. GitHub Pages 后台配置

> 执行位置：**GitHub 网页后台**

进入仓库后按这个路径操作：

1. 打开仓库
2. 点击 `Settings`
3. 左侧点击 `Pages`
4. 找到 `Build and deployment`
5. `Source` 选择 **GitHub Actions**

说明：

- 你现在不是手动上传 `public/`
- 而是通过 GitHub Actions 自动构建并自动部署
- 所以这里必须选 **GitHub Actions**

---

# 17. 需要额外配置“密钥”吗

> 执行位置：**GitHub 网页后台（通常无需手动操作）**

结论：

- **GitHub Pages 自动部署本身，一般不需要你手动创建仓库 Secret**
- 工作流会自动使用 GitHub 提供的 `GITHUB_TOKEN`
- 关键是工作流权限要写对：

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

你记得“好像配置过密钥”，大概率是把下面两件事混在一起了：

### 1. GitHub Actions 部署 Pages
这个通常**不用你自己手建 secret**

### 2. 本地 `git push` 认证
这个可能会涉及：

- 浏览器登录 GitHub
- 凭据管理器
- Personal Access Token（PAT）

这是**本地 Git 登录问题**，不是 GitHub Pages 部署本身的问题。

---

# 18. 第一次提交并推送

> 执行位置：**Git Bash**

在项目根目录执行：

```powershell
git add .
git commit -m "init hugo blog"
git push -u origin main
```

说明：

- `git add .`：把当前改动全部加入暂存区
- `git commit -m ...`：创建提交记录
- `git push -u origin main`：把本地 `main` 分支推送到 GitHub

---

# 19. 查看 Actions 是否部署成功

> 执行位置：**GitHub 网页后台**

步骤：

1. 打开仓库
2. 点击 `Actions`
3. 查看 `Deploy Hugo site to Pages`
4. 变成绿色说明构建成功
5. 成功后访问：

```text
https://uaenawook.github.io
```

---

# 20. HTTPS 设置

> 执行位置：**GitHub 网页后台**

如果你使用的是 `github.io` 默认域名，GitHub Pages 一般会自动支持 HTTPS。  
你还可以检查：

1. `Settings`
2. `Pages`
3. 查看是否可以勾选 `Enforce HTTPS`

如果能勾选，建议打开。

---

# 21. 以后日常写作流程

## 新建文章

> 执行位置：**Git Bash**

```powershell
hugo new posts/文章文件夹名/index.md
```

---

## 编辑正文

> 执行位置：**VS Code 编辑文件**

编辑：

```text
content/posts/文章文件夹名/index.md
```

---

## 放图片

> 执行位置：**资源管理器 / VS Code**

把图片放同目录：

```text
content/posts/文章文件夹名/cover.png
```

正文里写：

```md
![图](cover.png)
```

---

## 本地预览

> 执行位置：**Git Bash**

```powershell
hugo server -D
```

---

## 提交上线

> 执行位置：**Git Bash**

```powershell
git add .
git commit -m "add new post"
git push
```

push 后 GitHub Actions 会自动重新部署。

---

# 22. 最容易出错的地方

## 1）仓库名写错
用户站点仓库必须是：

```text
uaenawook.github.io
```

不是别的名字。

---

## 2）`baseURL` 写错
必须写成：

```toml
baseURL = "https://uaenawook.github.io/"
```

否则可能导致：

- 样式丢失
- 图片加载失败
- 跳转路径错误

---

## 3）主题名和目录名不一致
这句：

```toml
theme = "hugo-theme-monochrome"
```

必须和目录一致：

```text
themes/hugo-theme-monochrome
```

---

## 4）忘了拉子模块
工作流里要有：

```yaml
submodules: recursive
```

否则部署时主题可能拉不下来。

---

## 5）命令不在项目根目录执行
`hugo server -D`、`hugo new ...` 都要在 **项目根目录** 执行。  
也就是能看到：

```text
hugo.toml
```

的那个目录。

---

## 6）把 Pages 部署和本地 Git 认证混为一谈

### Pages 自动部署
靠：

- GitHub Actions
- `GITHUB_TOKEN`
- 正确的权限配置

### 本地 `git push`
靠：

- 本地 Git 登录状态
- 浏览器认证
- 凭据管理器
- 或 PAT

两者不是一回事。

---

# 23. 最短可执行清单

## GitHub 网页后台
1. 新建仓库：`uaenawook.github.io`

---

## Git Bash

```bash
mkdir uaenawook.github.io
cd uaenawook.github.io
git init
git branch -M main
git remote add origin https://github.com/uaenawook/uaenawook.github.io.git
hugo version
hugo new site . --force
git submodule add https://github.com/kaiiiz/hugo-theme-monochrome.git themes/hugo-theme-monochrome
```

---

## PowerShell

```powershell
winget install Hugo.Hugo.Extended
```

---

## VS Code
1. 改 `hugo.toml`
2. 建 `content/posts`、`content/solutions`、`content/moments`
3. 写 `archetypes/default.md`
4. 写 `.github/workflows/hugo.yml`

---

## GitHub Pages 后台
1. `Settings`
2. `Pages`
3. `Source = GitHub Actions`

---

## 提交上线（PowerShell 推荐）

```powershell
git add .
git commit -m "init"
git push -u origin main
```

---

# 24. 最终结论

这套流程最核心的规则只有这些：

1. GitHub 先建用户站点仓库：`uaenawook.github.io`
2. 本地项目根目录必须固定
3. Hugo 装 **extended**
4. 主题用 `git submodule`
5. 全站配置改 `hugo.toml`
6. 文章统一用一文一目录
7. 新建文章用 `hugo new 路径/index.md`
8. 本地先 `hugo server -D`
9. GitHub Pages 选择 **GitHub Actions**
10. push 后自动部署

按这套做，从 0 到上线是完整可执行的。
