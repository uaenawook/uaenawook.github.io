---
title: "写文章工作流" # 标题
date: 2026-03-23T15:10:47+08:00 # 发布时间
lastmod: 2026-03-23T15:10:47+08:00 # 修改时间，为了seo搜索，告诉google你修改了文章 
author: "uaena" #作者
categories: ["折腾"] # ["分类","分类2"]
tags: ["Hugo","2026"] # ["标签","标签2"]
description: "摘要"
draft: false # 草稿状态关闭=flase，开启为true
ShowToc: true # true=开启目录
TocOpen: false # false=目录展开
# 分类	标签
# 学习	C++, Python, 二叉树, LeetCode
# 折腾	Hugo, Git, 代理, Gemini
# 投资	A股, 美股, 加密货币, ETF
# 英语	口语, 词汇, 阅读
# 生活	CF, 洛克王国, 时间管理, 复盘
---

### 写文章工作流
1. 新建文件夹，并命名为文章标题
2. 在根目录下用CMD、PowerShell、Git Bash、VS Code 终端输入一下指令
    ```
    hugo new index.md
    ```

    输入完指令，文件会创建在uaenawook.github.io\\content\\index.md

3. 把文件拖进新建文件夹
4. 可以写文章啦！
5. 注意事项
    - 插入图片时可命好名
    - 图片用相对路径，即新建文件夹内
6. 引用其他.md文件</br>

    - 展示索引.md文件： [学习计划]({{< ref "posts/2026重启计划" >}})。

        ```
        在 Hugo 的 ref/relref 系统里，它会自动在 content 目录下全文搜索
        请参考我的 [学习计划]({{/*< ref "posts/2026重启计划" >*/}})。 
        记得去掉/*和*/
        ```
    - 展示索引文件：[阅读Git.txt](Git.txt)
    - 展示索引文件：[下载文件Git.7z](Git.7z)
        ```
        .txt、.zip、.cpp、.pdf文件会被认为是静态文件，可下载
        .md文件不可下载，会被解读为html
        展示索引文件：[下载文件Git.7z](Git.7z)
        ```

