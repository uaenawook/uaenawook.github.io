---
title: "{{ replace .Name "-" " " | title }}" # 标题
date: {{ .Date }} # 发布时间
lastmod: {{ .Date }} # 修改时间，为了seo搜索，告诉google你修改了文章 
author: "uaena" #作者
categories: ["分类"] # ["分类","分类2"]
tags: ["标签"] # ["标签","标签2"]
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
