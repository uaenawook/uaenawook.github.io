---
title: "此部署博客过程及遇到的问题" # 标题
date: 2026-03-23T18:59:14+08:00 # 发布时间
lastmod: 2026-03-23T18:59:14+08:00 # 修改时间，为了seo搜索，告诉google你修改了文章 
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

> **引言，此博客由ChatGPT结合我和Gemini的对话以及本站源码总结**



# Hugo 博客搭建与写作工作流

这篇文章只记录三件事：

1. 我这套博客用了什么技术
2. 每个关键文件是干什么的
3. 以后要改哪里、怎么改，以及我踩过的坑

---

## 一、技术栈

我的博客技术栈：

- **Hugo**：静态站点生成器
- **GitHub Pages**：托管博客网页
- **GitHub Actions**：自动构建和自动部署
- **Monochrome**：当前使用的 Hugo 主题
- **Git Submodule**：管理主题
- **Markdown**：写文章

这套方案的核心逻辑：

- 我写 Markdown
- Hugo 把 Markdown 转成网页
- push 到 GitHub 后，Actions 自动构建
- GitHub Pages 自动上线

---

## 二、标准文件树

```text
uaenawook.github.io/
├── .github/
│   └── workflows/
│       └── hugo.yml                # 自动部署
├── archetypes/
│   └── default.md                  # 新文章模板
├── assets/                         # 需要 Hugo 处理的资源
├── content/
│   ├── _index.md                   # 首页内容
│   ├── about.md                    # 关于页
│   ├── posts/                      # 正式文章
│   │   └── my-post/
│   │       ├── index.md            # 文章正文
│   │       └── cover.png           # 本文图片
│   ├── moments/                    # 说说
│   │   ├── _index.md               # 说说栏目页
│   │   └── 2026-03-22.md           # 单条说说
│   └── solutions/                  # 题解
├── static/
│   ├── img/                        # 静态图片
│   ├── avatar.jpg                  # 头像
│   └── favicon.ico                 # 网站图标
├── themes/
│   └── hugo-theme-monochrome/      # 主题
├── public/                         # Hugo 生成后的网页文件，不手改
├── resources/                      # Hugo 构建缓存
├── .gitmodules                     # submodule 配置
└── hugo.toml                       # 全站总配置
````

---

## 三、关键文件说明

### 1. `hugo.toml`

全站总配置文件。
博客的大部分全局行为都在这里控制。

常见内容包括：

* `baseURL`：网站地址
* `languageCode`：站点语言
* `title`：网站标题
* `theme`：当前主题名
* `[outputs]`：输出格式
* `[params]`：主题参数
* `[menu]`：导航菜单
* `[taxonomies]`：分类和标签
* `disableKinds`：关闭某些页面类型

### 2. `content/_index.md`

首页内容。
首页标题、首页文案、首页展示方式，优先改这里。

### 3. `content/about.md`

关于页内容。
个人介绍、自我说明，改这里。

### 4. `content/posts/`

正式文章目录。
推荐结构：**一篇文章一个文件夹**。

例如：

```text
content/posts/hugo-workflow/
├── index.md
└── cover.png
```

这样做的好处：

* 图片路径简单
* 附件和正文放一起
* 后期迁移最稳

### 5. `content/moments/`

“说说”栏目。
短内容一般一条一个 `.md`，栏目说明改 `_index.md`。

### 6. `archetypes/default.md`

新文章模板。
以后每次执行 `hugo new`，都会按这个模板生成 Front Matter。

### 7. `.github/workflows/hugo.yml`

自动部署配置。
push 后自动构建、自动发布。
部署失败优先看这个文件和 GitHub Actions 日志。

### 8. `static/`

不需要 Hugo 处理、只需要原样复制的静态文件。
例如头像、图标、下载附件。

### 9. `themes/hugo-theme-monochrome/`

主题目录。
当前整体布局、样式、模板主要来自这里。
如果以后真要改主题模板，通常会看：

* `themes/hugo-theme-monochrome/layouts/`
* 或自己在根目录新建 `layouts/` 做覆盖

---

## 四、`hugo.toml` 常用配置解释

下面是最常见的一份骨架：

```toml
baseURL = "https://uaenawook.github.io/"
languageCode = "zh-cn"
title = "UaenaWook Blog"
theme = "hugo-theme-monochrome"

[outputs]
home = ["HTML", "JSON"]

[params]
description = "个人博客"
navbar_title = "UaenaWook Blog"
footer = "Copyright © 2026 uaenawook"

[taxonomies]
category = "categories"
tag = "tags"

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
```

### 每个字段是什么意思

#### `baseURL`

网站根地址。

* 根域名仓库用：
  `https://uaenawook.github.io/`
* 项目页仓库用：
  `https://用户名.github.io/仓库名/`

这个字段错了，常见后果是：

* 图片丢失
* CSS 路径错
* 页面跳转错

#### `languageCode`

语言代码。
中文一般写：

```toml
languageCode = "zh-cn"
```

#### `title`

网站标题。
通常会显示在浏览器标题、部分主题页头等位置。

#### `theme`

主题目录名。
必须和 `themes/` 里的文件夹名一致。

#### `[outputs]`

控制 Hugo 生成哪些格式。
如果主题搜索依赖 JSON，一般首页要保留 JSON。

例如：

```toml
[outputs]
home = ["HTML", "JSON"]
```

如果这里没开 JSON，很多本地搜索会失效。聊天记录里你切 Monochrome 时，多次提到搜索依赖 JSON 输出。 

#### `[params]`

主题参数区。
不同主题字段不同，要看当前主题文档。

常见会放：

* 站点描述
* 导航标题
* 页脚
* 头像
* 搜索开关
* TOC 开关
* 数学公式开关

#### `[taxonomies]`

定义分类和标签。
常见写法：

```toml
[taxonomies]
category = "categories"
tag = "tags"
```

#### `[[menu.navbar]]`

导航菜单项。
一个菜单写一组。

#### `disableKinds`

关闭某些默认页面。
例如不需要 RSS 时可以关闭。

---

## 五、配置博客的标准顺序

### 第一步：初始化 Hugo 项目

在项目根目录执行：

```bash
hugo new site . --force
```

前提：你当前就在博客根目录。
聊天记录里也一直强调，Hugo 对目录结构很敏感。

### 第二步：安装主题

推荐 submodule：

```bash
git submodule add https://github.com/kaiiiz/hugo-theme-monochrome.git themes/hugo-theme-monochrome
```

好处：

* 主题和主仓库分离
* 后续更新方便
* 不会把主题源码全部手动混进来

聊天记录里你换主题时，核心方案也是 submodule。

### 第三步：配置 `hugo.toml`

先保证最小可运行：

* `baseURL`
* `title`
* `theme`
* `outputs`
* 基础 `params`
* 导航菜单

### 第四步：设置文章模板

改 `archetypes/default.md`。
这样以后新建文章时，自动带上统一头信息。

聊天记录里多次提到：**模板应交给 archetypes，而不是每次手写。** 

### 第五步：本地预览

在项目根目录执行：

```bash
hugo server -D
```

然后打开：

```text
http://localhost:1313
```

### 第六步：配置自动部署

创建：

```text
.github/workflows/hugo.yml
```

然后去 GitHub Pages 里把 Source 切到 **GitHub Actions**。
聊天记录里这一步也反复出现。 

### 第七步：推送上线

```bash
git add .
git commit -m "init blog"
git push origin main
```

---

## 六、正确的写文章工作流

### 推荐流程

不要先 `hugo new index.md` 再手动拖文件。
更稳的做法是直接创建到目标目录。

### 标准写法

```bash
hugo new posts/hugo-workflow/index.md
```

这条命令的含义：

* 在 `content/posts/` 下创建 `hugo-workflow/`
* 自动生成 `index.md`
* 自动套用 `archetypes/default.md`

如果是题解：

```bash
hugo new solutions/binary-tree/index.md
```

如果是说说：

```bash
hugo new moments/2026-03-23.md
```

### 写图和附件

如果文章结构是：

```text
content/posts/hugo-workflow/
├── index.md
└── cover.png
```

那正文直接写：

```md
![封面](cover.png)
```

不要写本地绝对路径。
聊天记录里也明确提到：图片引用要用同目录相对路径，不要写死本地路径。 

---

## 七、Front Matter 应该怎么写

最小可用模板：

```yaml
---
title: "文章标题"
date: 2026-03-23T10:00:00+08:00
draft: false
categories: ["博客"]
tags: ["Hugo"]
description: "一句话描述"
---
```

### 必留字段

* `title`
* `date`
* `draft`

### 推荐字段

* `categories`
* `tags`
* `description`

聊天记录里你已经踩过：
不能只剩 `title`，`date` 和前后的 `---` 也必须保留，否则排序或解析会出问题。

---

## 八、以后想改什么，去哪里改

### 1. 改网站标题、导航、页脚、搜索

去：

```text
hugo.toml
```

### 2. 改首页标题、首页文案、首页展示内容

去：

```text
content/_index.md
```

### 3. 改关于页

去：

```text
content/about.md
```

### 4. 改文章

去：

```text
content/posts/某篇文章/index.md
```

### 5. 改说说栏目说明

去：

```text
content/moments/_index.md
```

### 6. 改新文章默认模板

去：

```text
archetypes/default.md
```

### 7. 改自动部署逻辑

去：

```text
.github/workflows/hugo.yml
```

### 8. 改整体模板和布局

优先看：

```text
themes/hugo-theme-monochrome/layouts/
```

更推荐的做法是：
在项目根目录新建 `layouts/`，用同路径文件覆盖主题模板，避免直接乱改主题源码。

---

## 九、我遇到的主要问题与解决方案

### 1. 仓库名和访问地址没理顺

问题：

* 普通仓库会变成 `用户名.github.io/仓库名`
* 根域名仓库必须叫 `用户名.github.io`

解决：

* 想直接访问 `https://uaenawook.github.io/`
* 仓库名就必须是 `uaenawook.github.io`

### 2. 把 GitHub 默认域名和 Custom domain 搞混了

问题：

* 误以为 `Custom domain` 可以填 GitHub 默认地址

解决：

* `Custom domain` 只给自定义域名用
* GitHub 默认地址不用填这个

### 3. 主题切换后搜索不生效

问题：

* 主题依赖首页 JSON 输出
* `outputs` 没配对就会失效

解决：

```toml
[outputs]
home = ["HTML", "JSON"]
```

聊天记录里多次强调了这一点。

### 4. `hugo server -D` 报错

常见原因：

* 不在项目根目录执行
* `theme` 名称和目录名不一致
* 旧主题配置残留
* Hugo 版本不对
* submodule 没拉下来

解决：

* 确认当前目录有 `hugo.toml`
* 检查 `theme = ""`
* 清理旧配置
* 检查主题目录
* 看 Actions / 本地报错日志

### 5. `hugo new` 执行位置错了

问题：

* 在 `content/solutions` 里执行 Hugo 命令，报找不到项目目录

解决：

* **必须在项目根目录执行**
* 也就是能看到 `hugo.toml` 的目录

这是你聊天里很明确踩过的坑。

### 6. `ref / relref` 写错成了 `rel`

问题：

* 写成了 Hugo 不认识的 shortcode，直接报错

解决：

```md
{{< ref "posts/2026重启计划" >}}
```

或：

```md
{{< relref "posts/2026重启计划" >}}
```

聊天记录里这点非常明确。

### 7. 在文章里展示 shortcode 代码时被 Hugo 直接执行了

问题：

* 代码块里写 `{{/*< ref ... >*/}}`，网页没有按代码显示，而是被执行

解决：

```md
{{/*< ref "posts/2026重启计划" >*/}}
```

也就是加注释转义。
你已经明确踩过这个坑。

### 8. Front Matter 插件和 Hugo 模板不一致

问题：

* FM 插件新建文章，不按 `archetypes/default.md` 来

原因：

* 它是两套系统
* Hugo 原生命令走 `archetypes`
* FM 插件走自己的 `frontmatter.json`

解决：

* **新建文章优先用 `hugo new`**
* FM 插件只作为辅助管理工具

这也是你聊天里很明确的结论。

### 9. VS Code 终端卡住、不能输入

问题：

* 终端进程卡死，或者焦点不对

解决：

* Kill Terminal
* 新建终端
* 换 Shell
* 直接用系统终端

这点你也踩过。

---

## 十、最终结论

这套博客最核心的规则只有几条：

1. **全站配置看 `hugo.toml`**
2. **首页改 `content/_index.md`**
3. **文章用一文一目录**
4. **新建文章用 `hugo new 路径/index.md`**
5. **图片和附件跟文章放同目录**
6. **本地先 `hugo server -D` 检查**
7. **上线靠 GitHub Actions 自动部署**
8. **遇到链接问题优先用 `ref / relref`**
9. **遇到模板问题先分清是 Hugo 原生还是 VS Code 插件**
10. **所有 Hugo 命令都在项目根目录执行**

做到这 10 条，后面维护博客基本不会乱。

